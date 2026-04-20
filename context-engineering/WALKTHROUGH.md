# Notebook Walkthrough

A plain-English explanation of every cell in `context-engineering-playground.ipynb`. Read this to understand what the notebook does without running it.

---

## Section 1 — Setup

**Cell 1: Install packages**

```python
%pip install anthropic numpy --quiet
```

Installs two libraries into your active Python environment: `anthropic` (the SDK for calling Claude's API) and `numpy` (used for math in the RAG section). The `--quiet` flag suppresses most of the install output. This only needs to run once per environment.

---

**Cell 2: Initialize the client and define helpers**

This is the foundation everything else builds on. It does four things:

1. **Creates the API client** using your `ANTHROPIC_API_KEY` environment variable. If the key isn't set, it falls back to the string `"YOUR_KEY_HERE"` and all API calls will fail with an auth error.

2. **Sets the model** to `claude-haiku-4-5` — Anthropic's fastest and cheapest model. You can change this line to use Sonnet or Opus for higher-quality outputs.

3. **Defines `chat(messages, system, max_tokens)`** — a thin wrapper around the Anthropic API. Instead of writing 5 lines of boilerplate every time you want to make an API call, you call `chat(...)` with a list of messages and get back the model's reply as a plain string. The `system` parameter sets the system prompt (instructions that frame the model's role). `max_tokens` caps how long the response can be.

4. **Defines two utility functions**: `token_count(text)` estimates token usage by dividing character count by 4 (a rough but useful approximation for English text), and `wrap(text)` word-wraps long strings for readable terminal output.

---

## Section 2 — Context Rot Demo

**The concept:** "Needle in a haystack" (NIAH) testing. A specific fact — the needle — is planted inside a large block of filler text. The model is asked to retrieve it. As the filler grows, retrieval accuracy drops, even though the answer is still present in the context window. This is *context rot*: the model's ability to use information degrades as the window fills up.

---

**Cell 3: Define the needle, filler, and NIAH sweep**

Sets up three things:

- `NEEDLE` — a single invented fact: *"The internal project codename for the 2031 budget cycle is GOLDFISH."* This is the answer the model must find.
- `FILLER` — a plausible-sounding paragraph about infrastructure operations. It's realistic-looking text that has nothing to do with the needle.
- `build_haystack(needle, filler, copies, position)` — assembles the full prompt by repeating the filler paragraph `copies` times, then burying the needle at the `start`, `middle`, or `end` of that block.
- `run_niah(copies_list, position, trials)` — runs the experiment across different filler sizes. For each size, it sends the haystack + question to the model `trials` times, counts how many times it gets "GOLDFISH" back, and prints a table of results.

The sweep runs with `[0, 5, 15, 30, 60]` copies. At 0 copies the answer is right there — expect 3/3. By 60 copies the context is thousands of tokens deep — expect degradation.

**What to look for:** the `Correct` column dropping as `Copies` grows. A 3/3 at 0 copies and a 1/3 or 0/3 at 60 copies would confirm context rot.

---

**Cell 4: Exercise 2-A — Position bias**

Runs the same test at a fixed size (20 copies) but varies where the needle is placed: `start`, `middle`, `end`.

**What to look for:** Middle placement is typically hardest — models have primacy (beginning) and recency (end) biases, so facts buried in the middle of a long context are most likely to be missed.

---

**Cell 5: Exercise 2-B — Coherent vs. shuffled haystack**

Compares two versions of the filler: the original coherent paragraph, and the same paragraph with its words randomly shuffled into nonsense.

**What to look for:** Counterintuitively, Chroma's research found that shuffled (incoherent) filler sometimes produces *better* retrieval than coherent filler. The hypothesis is that coherent text creates stronger competing patterns in the model's attention — it has to work harder to ignore something that reads like real content than to ignore obvious nonsense.

---

## Section 3 — Failure Mode Triggers

Each cell in this section deliberately induces one failure mode, then shows a mitigation in the following cell.

---

**Cell 6: Context Poisoning — trigger**

Context poisoning happens when a hallucination from one turn gets injected back into the conversation as if it were a verified fact, poisoning all subsequent reasoning.

The cell asks about the port number used by a fictional tool called "AcmeCorp DataRouter v3." This tool doesn't exist, so the model will invent a port number. That hallucinated answer is then fed back into a second message as established fact: "Given that port, what firewall rule should we add?" The model now treats the fabricated port as ground truth and builds further (equally fabricated) reasoning on top of it.

**What to look for:** The model confidently recommending a specific firewall rule based on a port number it made up moments earlier.

---

**Cell 7: Context Poisoning — mitigation**

Takes the same hallucinated answer from Cell 6 and wraps it in an `[UNVERIFIED — may be hallucinated]` tag before injecting it back. The system prompt instructs the model that anything tagged `[UNVERIFIED]` must be caveated in downstream recommendations.

**What to look for:** The model adding hedging language like "assuming the port is correct" or "you should verify this before applying it." The same information, but now the uncertainty is propagated forward instead of hidden.

---

**Cell 8: Context Clash — trigger and mitigation in one cell**

Context clash happens when the context window contains contradictory information about the same fact. The cell sends three "documents" that each state a different deployment timeout value (300s, 120s, 600s) with different dates.

First it asks with no guidance — the model has to guess which source to trust. Then it asks again with a system prompt rule: *"prefer the most recent source by date, and state which source you chose."*

**What to look for:** The first call may hedge, pick arbitrarily, or average the values. The second call should pick the most recent (Slack, 600s) and explicitly say why.

---

**Cell 9: Entity Resolution Failure — trigger and mitigation in one cell**

Entity resolution failure happens when two different things share the same name. The context describes two projects both called "Project Alpha" — one is an infrastructure initiative, one is a mobile app — then asks "how many engineers are on Project Alpha?"

The mitigation replaces the ambiguous shared name with unique IDs (`INFRA-001`, `MOBILE-002`) and re-asks the question using the specific ID.

**What to look for:** The first call either guesses, asks for clarification, or conflates the two projects. The second call answers correctly and unambiguously because the question references a unique identifier.

---

## Section 4 — The Four Operations

This section implements each of the four core context management operations from LangChain's framework: Write, Select, Compress, and Isolate.

---

**Cell 10: WRITE — External memory store**

Demonstrates saving state *outside* the context window so it survives across sessions.

The `Scratchpad` class simulates an external key-value store (in production this would be Redis, a database, or a vector store). Session 1 sends a message telling the model the user's name and formatting preference, then saves those facts to the scratchpad directly. Session 2 starts with a completely empty context window — no conversation history — but loads the saved values and injects them into the system prompt before calling the model.

**What to look for:** The Session 2 response using the user's name and formatting preference even though the context window contains no conversation history. The memory persisted outside the window.

---

**Cell 11: SELECT — Pull only relevant chunks**

Demonstrates retrieving only the relevant subset of a knowledge base, rather than dumping the entire thing into context.

A small knowledge base (`KB`) has 5 entries covering deployment, auth, database, monitoring, and security. The `keyword_select` function scores each entry by how many words it shares with the query, then returns the top 2. Only those 2 entries are injected into the context for the API call — the other 3 never touch the context window.

**What to look for:** The "Selected 2/5 chunks" output showing which entries were chosen, and the answer being correctly grounded in only those chunks. This is the core mechanism behind RAG — Section 5 extends it with proper relevance scoring.

---

**Cell 12: COMPRESS — Summarize older turns**

Demonstrates reclaiming token budget by replacing verbose conversation history with a compact summary.

A pre-written 6-message conversation history is split: the 4 oldest messages are sent to the model to summarize into 2 sentences, and the 2 most recent messages are kept verbatim. The output shows the token count before and after, and prints the summary.

**What to look for:** The summary being much shorter than the original transcript while preserving the key facts. Section 7 extends this into a live `RollingChat` class that does this automatically during a real conversation.

---

**Cell 13: ISOLATE — Separate context windows per agent**

Demonstrates splitting a problem across multiple agents, each with a focused context, rather than having one agent see everything.

A proposal to migrate an auth service is sent to two agents in parallel. The **Critic** agent has a system prompt instructing it to focus only on technical risks and ignore business considerations. The **Advocate** agent has a system prompt instructing it to focus only on shipping velocity and ignore edge cases. Neither agent sees the other's output. A third **Coordinator** agent then receives only the two reports (not the original proposal, not the agent histories) and synthesizes them.

**What to look for:** The Critic and Advocate giving sharply different responses because of their isolated, constrained contexts. The Coordinator's synthesis being more balanced than either alone.

---

## Section 5 — RAG Pattern

A more complete implementation of Retrieval-Augmented Generation than the SELECT operation above.

---

**Cell 14: Define corpus, ranker, and run queries**

Builds a small 8-document corpus covering deployment strategies, auth, secrets, and database operations.

`rank_docs(query, docs, top_k)` uses Claude itself as a relevance scorer: it sends all documents to the model and asks it to score each one 0–10 for relevance to the query, returning a JSON object. The top-K documents by score are retrieved. (A production system would use cosine similarity over text embeddings — faster, cheaper, and more reliable — but this approach avoids adding another library or API.)

`rag_answer(query, corpus, top_k)` chains retrieval and generation: retrieve the most relevant docs, build a context string from them, then call the model with a system prompt that says "answer only from the provided context."

Three queries are run: deployment differences, JWT security, and PgBouncer prepared statements.

**What to look for:** The retrieved chunk IDs matching the query topic, and the answers being grounded in those specific chunks. Each answer should be different because different docs were retrieved for each query.

---

**Cell 15: Exercise 5-A — Out-of-scope query**

Asks a question the corpus has no answer for: parental leave policy.

**What to look for:** The model saying it can't answer from the provided context, rather than hallucinating a policy. This is what the `"answer only from context"` system prompt is designed to enforce. If it works correctly, the model should explicitly decline rather than invent an answer.

---

## Section 6 — Multi-Agent Isolation

---

**Cell 16: Isolated Critic and Advocate**

Defines a reusable `Agent` class that maintains its own conversation history and system prompt, separate from all other agents.

Creates a Critic (focused purely on technical risk, explicitly told to ignore cost and business factors) and an Advocate (focused purely on shipping speed, explicitly told to ignore edge cases). Both evaluate the same proposal — a JWT microservice migration in 2 weeks — in complete isolation from each other.

**What to look for:** The two outputs being genuinely different in tone and content. The Critic should surface risks the Advocate glosses over. The Advocate should propose shortcuts the Critic would flag. This divergence is the point — isolation lets each agent reason without being anchored by the other's framing.

---

**Cell 17: Exercise 6-A — Anchoring contamination**

Creates a new Critic agent with the exact same system prompt as the isolated Critic, but this time shows it the Advocate's output before asking for its risk assessment.

**What to look for:** The contaminated Critic's critique being softer, shorter, or more aligned with the Advocate's optimistic framing than the isolated Critic's was. This is anchoring bias: the first piece of information you see shapes how you evaluate everything that follows. Isolation is what prevents this in multi-agent systems.

---

## Section 7 — Compression / Summarization

Two strategies for managing context length in long-running conversations.

---

**Cell 18: 7-A — Rolling summary chat**

Implements `RollingChat`, a class that automatically compresses the conversation as it grows. The parameters:

- `compress_every=4` — once the recent history reaches 4 messages, trigger compression
- `keep_recent=2` — always keep the 2 most recent messages verbatim; compress everything older

When compression triggers, the older messages are summarized into 2 sentences by a separate API call. That summary replaces the messages in the history and is injected into the system prompt on future turns so the model still has the context — just in compressed form.

A 7-turn conversation about Tanzu Application Service components is run through `RollingChat`. After each turn, the stats line shows how many messages are in recent history, how many compressions have happened, and the approximate token count.

**What to look for:** The `compressions` counter incrementing, the `recent` message count staying bounded (never growing past the `compress_every` limit), and the total token count staying roughly flat after the first compression rather than growing linearly. The model's answers should remain coherent across the full conversation despite the compressed history.

---

**Cell 19: 7-B — Checkpoint reset**

A more aggressive compression strategy: instead of summarizing prose, extract a structured JSON state object from the conversation and discard all history entirely.

`extract_state(history)` sends the conversation to the model and asks it to return a JSON object with four fields: `topic`, `decisions` (a list), `open_questions` (a list), and `key_facts` (a dict). This structured snapshot is then injected into the system prompt of a completely fresh session with an empty history.

**What to look for:** The structured JSON state printed after extraction — it should capture the essence of the TAS conversation from Cell 18 in a compact, machine-readable form. Then the post-reset response, which should be coherent and contextually appropriate even though the model has no conversation history, only the JSON checkpoint. The final line shows the token count of the state object vs. what the full history would have cost.

---

## Section 8 — Reference Tables

The final two cells are pure markdown — no code, no API calls.

**Pattern → Failure Mode Map:** A table mapping each mitigation pattern from the notebook (uncertainty tagging, RAG, conflict-resolution rules, disambiguating IDs, rolling summary, checkpoint reset, agent isolation, external memory) to the failure mode it addresses and the section where it's demonstrated.

**Decision Guide:** A simple rule of thumb for when context engineering is worth the investment (agent loops, multi-session continuity, large knowledge bases, multi-agent collaboration) vs. when simple prompting is sufficient (single-turn tasks, short conversations, self-contained context).
