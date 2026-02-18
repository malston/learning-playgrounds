# AI Agent Architecture Playground

Build an interactive learning playground that teaches how AI agents actually work — the architecture, the patterns, and the real engineering decisions — through hands-on exploration. No buzzwords. No marketing copy.

## Learning Goals

By the end, the learner should be able to:

- Explain precisely what makes an agent different from a chatbot (the loop, tool use, and state)
- Recognize the 4 core patterns in the wild and know when to reach for each
- Make an informed decision between single-agent and multi-agent architectures
- Read or sketch a rough implementation of each pattern in pseudocode or Python

## Core Interactive Elements

### 1. Agent vs. Chatbot: The Mental Model

A side-by-side interactive comparison showing a chatbot and an agent handling the same request. Animate the difference:

- Chatbot: single prompt → single response, stateless
- Agent: perceive → reason → act → observe → repeat (the loop)

Let the user step through the agent loop one step at a time to see what happens at each phase. Show the actual data structures: what's in the context window, what tool calls look like, what the observation feeds back in.

### 2. The 4 Patterns — Interactive Pattern Cards

Each pattern gets its own card with:

- A one-sentence definition (the "what")
- When to use it vs. when not to (the "why")
- An animated flow diagram showing how data and control move
- A minimal Python pseudocode snippet showing the skeleton
- A real-world example (e.g., routing → customer support triage; orchestration → research pipeline; reflection → code review agent)

**The 4 patterns:**

- **Prompt Chaining** — sequential steps, output of one feeds the next
- **Routing** — classify input, dispatch to the right handler
- **Parallelization** — fan out to multiple workers, aggregate results
- **Orchestrator / Subagent** — one agent plans and delegates to specialized subagents

Include a comparison table at the end of this section: latency, complexity, failure modes, and best use case for each.

### 3. Single vs. Multi-Agent Decision Tool

An interactive decision tree. User answers 4–5 questions:

- How complex is the task? (single clear goal vs. multiple interdependent subtasks)
- Does it require specialized expertise in different domains?
- What are the latency requirements?
- How important is auditability / traceability?

Output: recommended architecture with a brief explanation of why. Show the failure modes of the wrong choice (e.g., "if you go multi-agent here, here's what breaks").

### 4. Pattern → Code Mapping

A tabbed code explorer showing each pattern implemented as a minimal but real Python skeleton using the Anthropic SDK. Not pseudocode — actual runnable structure with comments explaining each part. Show:

- The core loop
- How tool calls are structured
- Where state lives
- How errors propagate

Let the user toggle between patterns to compare implementations side by side.

### 5. Build Your Own Agent Flow

A simple drag-and-drop or click-to-connect canvas where the user assembles an agent architecture from components (LLM call, tool, router, subagent, memory). When they connect components, show the data flow and flag architectural problems (e.g., "this creates a cycle with no exit condition").

## Style & UX

- Dark theme, dense but not cluttered
- Diagrams should animate on first view, then be interactive on demand
- Code snippets use syntax highlighting, are copyable
- Key terms have hover tooltips with a single-sentence definition
- No quizzes — this is a reference and exploration tool for engineers
- Sections are self-contained but flow logically top to bottom

## Audience

Software engineers and platform architects who have used LLMs via API but haven't built agents. They understand HTTP, async programming, and distributed systems. They're skeptical of hype and want the engineering reality, not the demo.
