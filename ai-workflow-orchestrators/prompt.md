# Development Workflow Orchestrators Playground

Build an interactive decision-making playground for choosing and combining AI development workflow orchestrators. The four tools covered are **GSD**, **Superpowers**, **Conductor**, and **Spec Kit** — an emerging category that sits between coding assistants (Claude Code, Cursor, Copilot) and general agent frameworks (LangChain, CrewAI).

The audience is senior engineers and tech leads who are already using AI coding assistants and want to add structured workflow orchestration. They understand software development processes, git, and multi-agent concepts. They don't need definitions of basic terms — they need help distinguishing between four tools that look similar on the surface and making a defensible choice for their context.

---

## Core Interactive Elements

### 1. Orchestrator Selector — Guided Questionnaire

A multi-step wizard that produces a ranked recommendation. Questions should feel like a conversation with a principal engineer who has used all four tools, not a feature checklist. Each answer narrows the option space and explains why.

**Questions in sequence (branch based on answers):**

1. **What's your primary pain point with AI-assisted development today?**
   - We build the wrong thing (vague requirements turn into wasted sprints)
   - We build it sloppily (skipped tests, inconsistent quality, shortcuts under pressure)
   - We can't maintain momentum on large projects (context gets lost across sessions)
   - We can't prove what we did or why (compliance, audits, team turnover)

2. **How large and long-running are the projects you're orchestrating?**
   - Single features or bug fixes (hours to days)
   - Multi-phase projects (weeks, multiple milestones)
   - Large codebases where the project outlives the context window

3. **Is TDD non-negotiable on your team?**
   - Yes — we need structural enforcement, not just encouragement
   - We practice it but don't need guardrails
   - Not a priority

4. **What's your compliance and audit situation?**
   - We need to prove process compliance to external auditors
   - We need internal traceability (requirements → implementation → verification)
   - Neither applies

5. **Do you need this to work across multiple AI coding assistants?**
   - Yes — our team uses different tools (Cursor, Copilot, Claude Code, etc.)
   - No — we're standardized on one

6. **Where does your team fall on the AI-assisted dev maturity curve?**
   - New to it — we need guardrails and mandatory gates
   - Building confidence — structure helps but we can handle some autonomy
   - Experienced — we want discipline without lifecycle overhead

**Output:** Primary recommendation + 1 complementary tool to stack on top, each with:

- The specific answers that drove the recommendation
- The 2 tradeoffs they're accepting
- A warning if the answer pattern suggests a common mistake (e.g., choosing Conductor for a team that needs cross-phase requirements traceability, or choosing GSD for a team that needs mandatory TDD gates)

Show the elimination logic — which answers ruled out which tools and why.

---

### 2. Context Management Visualizer

The most important and least understood distinction between these tools. This section makes it visceral.

**The setup:** A growing codebase (show as a block representation of files/tokens). A slider that grows the codebase from 10K tokens → 100K → 500K → 2M tokens. A task to execute: "Add OAuth provider support to the auth module."

**For each tool, animate what happens:**

- **GSD** — The orchestrator identifies which files are relevant to the task (auth module, config, tests, API contracts). A fresh 200K context window opens containing only those files + the task XML block + phase context. The rest of the codebase is not loaded. Show what the executor "sees" vs. what exists.

- **Superpowers** — A fresh subagent is dispatched with the full task text from the controlling session. No selective file loading — the controlling session provides context. Show what gets passed and what doesn't.

- **Conductor / Spec Kit** — The task runs in the host session's shared context. Show the context window filling up over the course of a long track. At 2M codebase size, show the context window saturating — earlier decisions getting evicted, file contents no longer in scope.

**The insight to make explicit:** Context scheduling is an information retrieval problem that scales with project size, not model capability. Larger context windows help but don't eliminate the need for orchestrators to decide what's relevant. Label this clearly.

---

### 3. The Flexibility Spectrum — Interactive Explorer

The four tools span a spectrum from flexible to opinionated. Make this navigable.

```text
Flexible ←————————————————————————————————→ Opinionated
    Superpowers    Spec Kit    GSD    Conductor
```

**Clicking a tool on the spectrum reveals three panels:**

**What it enforces** (the constraints you accept):

- Superpowers: coding discipline per task (TDD, systematic debugging, two-stage review)
- Spec Kit: specification quality and constitutional compliance before implementation
- GSD: full project lifecycle (milestone → phase → plan → task → verification)
- Conductor: task-level TDD lifecycle (mandatory 11 steps), manual phase gates, git audit trail

**What it leaves to you** (where you retain control):

- Superpowers: project structure, branching strategy, milestone cadence, everything above task level
- Spec Kit: execution approach, task sequencing, verification
- GSD: coding style, TDD enforcement within tasks, commit granularity
- Conductor: project-level milestone planning, cross-track integration

**What breaks if you choose wrong:**

- Superpowers on a team that needs project-level structure → discipline without direction
- Spec Kit on a team that needs execution rigor → great specs, inconsistent implementation
- GSD on a team that needs mandatory TDD gates → structure without task-level enforcement
- Conductor on a project that needs cross-phase integration checking → track isolation becomes a blind spot

---

### 4. What Survives Better Models

The most distinctive and forward-looking section. Make it interactive.

**The premise:** Language models are improving rapidly. Some orchestrator capabilities become less valuable as models improve at self-correction, planning, and code generation. Others become more valuable or remain stable regardless of model capability.

**An interactive matrix** where the user can drag a "model capability" slider from Today → 2 years → 5 years and watch the value of each capability category shift:

| Capability | Today | 2 Years | 5 Years |
| --- | --- | --- | --- |
| Discipline enforcement (TDD gates, anti-patterns) | High | Medium | Low-Medium |
| Context scheduling (selective loading, fresh windows) | High | High | High |
| Audit trails (git notes, requirements traceability) | High | High | High |
| Specification quality (spec → clarify → analyze) | High | High | High |
| Execution parallelism (wave-based dispatch) | High | High | Medium-High |

**For each capability, explain the trajectory:**

- **Discipline enforcement** weakens because models are getting better at writing tests first and avoiding shortcuts without structural enforcement. The gap between "with hard gates" and "without" narrows with each generation.
- **Context scheduling** strengthens because projects grow with time, not with model capability. A 2M-token codebase today will be a 3M-token codebase next year.
- **Audit trails** are stable — compliance requirements don't relax because models improve. AI-generated code faces *more* scrutiny as adoption grows, not less.
- **Specification quality** is durable — garbage in, garbage out regardless of model capability. A model 10x better at code generation still builds the wrong thing if the spec is vague.

**The punchline:** Tools that bundle multiple capabilities (GSD: context scheduling + audit trails + discipline) are more durable than single-capability tools. Show which tools gain, hold, or lose relative advantage as the slider moves.

---

### 5. Combination Recommender

Many teams run two tools in combination. Show the valid combinations, what each pairing achieves, and which pairings don't make sense.

**User selects their primary tool.** The interface shows:

**Compatible combinations** with a one-line explanation of why they stack cleanly:

- GSD + Superpowers → project lifecycle (GSD) + per-task coding discipline (Superpowers). They don't conflict because GSD orchestrates phases while Superpowers operates within individual tasks.
- Conductor + Superpowers → Conductor's audit trail + Superpowers' brainstorming and debugging skills. Superpowers fills the skill gaps Conductor doesn't cover.
- Spec Kit + GSD → Spec Kit's specification pipeline feeds GSD's execution engine. Strongest combination for projects that need both spec quality and parallel execution throughput.
- Spec Kit + Conductor → Spec Kit produces the spec; Conductor enforces TDD execution with audit trails.
- Spec Kit + Superpowers → Lightweight: good specs with flexible execution discipline.

**Pairings to avoid** with explanations:

- GSD + Conductor → Both manage full lifecycle; they conflict on who owns the workflow. Choose one.
- Multiple heavy orchestrators → Lifecycle management doesn't compose. Discipline tools (Superpowers) compose; lifecycle frameworks don't.

**Enterprise maturity progression** — show how the right combination evolves as team maturity grows:

| Stage | Combination | Rationale |
| --- | --- | --- |
| New to AI-assisted dev | Conductor or GSD alone | Mandatory gates prevent costly mistakes |
| Building confidence | GSD + Superpowers | Project structure + per-task discipline, gates optional |
| Experienced team | Superpowers or Spec Kit alone | Discipline without lifecycle overhead |
| Mixed maturity team | Spec Kit + team-appropriate execution | Standardize on specs, vary execution flexibility |

---

### 6. Reconstructability Matrix — What Questions Can You Answer Later?

Frame this as: "Six months from now, when the original developer is gone, what questions can you answer about how this codebase was built?"

**An interactive table where the user checks which questions matter to them:**

| Question | Best Tool | Mechanism |
| --- | --- | --- |
| Was every requirement implemented and verified? | GSD | REQUIREMENTS.md → VERIFICATION.md cross-reference with orphan detection |
| How was this built, step by step? | Conductor | Git notes on every task and phase, queryable via `git log --notes` |
| Why was this built this way, not another? | Spec Kit | research.md captures alternatives considered and decisions made |
| Did the team follow their own standards? | Conductor + Superpowers | Style guide review + anti-pattern detection |
| What was the original intent of this feature? | Spec Kit | spec.md with user stories, acceptance criteria, and edge cases |

**The insight to surface explicitly:** Most teams capture *compliance* (did we do it?) or *execution* (how did we do it?) but not *intent* (why did we choose this approach over alternatives?). Intent is the hardest to reconstruct and the most valuable when it's gone. Spec Kit is the only tool that systematically captures decision rationale.

Checking boxes in this table should update a recommendation that emphasizes which tools produce the artifacts for those questions.

---

## Style & UX

- Dark theme — `#0a0e17` background, `#38bdf8` / `#a78bfa` accents, `#34d399` / `#fb923c` secondary accents
- The context management visualizer should animate — the filling context window and selective loading effect are the key insight, make them visible
- The "What Survives Better Models" slider should update the matrix in real-time
- Wizard steps feel progressive — don't show all questions at once, each answer narrows visibly
- Every recommendation exposes its reasoning — show which answers drove which conclusions
- Tooltips on terms: context window, git notes, wave-based execution, constitutional governance, TDD hard gate
- The flexibility spectrum should be a horizontal interactive element, not a table

## What This Is Not

- Not a tutorial on how to install or configure any of these tools
- Not a feature list — the cheatsheet already covers features exhaustively
- Not a benchmark — focus on architectural tradeoffs, not performance numbers
- Not opinionated about one right answer — the tools genuinely solve different problems for different team contexts
- Not a comparison of coding assistants (Claude Code, Cursor, Copilot) — those are the layer below; this playground covers the orchestration layer above them
