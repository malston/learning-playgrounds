# Agent Harness Decision Framework

Build an interactive decision playground for choosing between third-party agent frameworks (BMAD, GSD, Superpowers) and building on Anthropic's native harness design. Based on Anthropic's ablation experiments with Opus 4.6 and direct analysis of each framework's architecture, provider strategy, and update velocity.

The audience is senior engineers and tech leads who are already using AI coding agents and need to decide whether to adopt a third-party framework, build on native capabilities, or compose a hybrid. They understand agent patterns, context windows, and multi-agent concepts. They want architectural reasoning, not feature lists.

The core question this playground answers: **does a third-party harness add value over what the model can do natively, or does it encode stale assumptions that slow you down?**

---

## Core Interactive Elements

### 1. Ablation Explorer

An interactive visualization of Anthropic's harness ablation experiments. Start with the full harness (planner + generator + evaluator + sprint decomposition + context resets + detailed task specs + sprint contracts), then let the user toggle each component on and off.

**For each component, show two columns:**

| Component Removed    | Impact on Opus 4.6                                         | Impact on Sonnet 4.5                           |
| -------------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| Sprint decomposition | None -- model maintained coherence without task breakdown  | Significant -- lost coherence on long tasks    |
| Context resets       | None -- no "context anxiety" observed                      | Significant -- premature task completion       |
| Detailed task specs  | Negative -- micro-specs caused cascading errors when wrong | Positive -- model needed step-by-step guidance |
| Sprint contract      | Minimal -- generator handled quality on its own            | Moderate -- evaluator had to catch more gaps   |
| Evaluator agent      | Significant for subjective tasks (UI, UX)                  | Significant across all task types              |
| Planner agent        | Significant -- generators under-scoped without it          | Significant                                    |

When a component is toggled off, animate its removal and highlight the impact. Color-code: green for "no impact" (safe to remove), amber for "moderate", red for "significant loss". The visual should make the surviving core obvious: **planner, generator, evaluator.**

Surface the key insight: every harness component encodes an assumption about what the model cannot do on its own. These assumptions go stale as models improve.

---

### 2. Framework Architecture Comparison

Side-by-side architecture diagrams for four approaches. Each diagram should be an interactive node-and-arrow layout (not static ASCII) that users can click to explore.

**The four architectures:**

**BMAD (Documentation-First):** Analyst -> Architect -> PM -> Developer -> QA. 12+ agents, heavy docs. File-based handoffs between agents.

**GSD (Spec-Driven):** Orchestrator dispatches to Researcher (4 agents) and Planner + Checker, then Parallel Executors (wave-based), then Verifier. 5 phases, context isolation.

**Superpowers (Skills-Based):** Brainstorm (Socratic) -> Plan (2-5 min tasks) -> Execute (subagents) -> TDD + Code Review. 7 stages, skill composition.

**Anthropic Native:** Planner -> Generator <-> Evaluator (graded scoring). 3 agents, agent teams (agents communicate directly).

**Clicking any node reveals:**

- What that agent/phase does
- What data flows in and out
- Whether communication is file-based or direct

**A comparison strip below the diagrams:**

| Dimension           | BMAD                                            | GSD                                        | Superpowers                              | Anthropic Native                            |
| ------------------- | ----------------------------------------------- | ------------------------------------------ | ---------------------------------------- | ------------------------------------------- |
| Provider target     | Agnostic (Claude, GPT, Gemini, Grok)            | Agnostic (9+ runtimes)                     | Claude-first, multi-provider added       | Claude-only                                 |
| Core value prop     | Specialized domain agents for requirements      | Context isolation + parallel waves         | TDD enforcement + methodology discipline | Graded evaluation + minimal scaffolding     |
| Planning depth      | Heavy: PRD -> architecture -> stories -> tasks  | Medium: research -> atomic XML plans       | Medium: brainstorm -> 2-5 min tasks      | Light: product-level spec only              |
| Evaluation method   | Code review + QA agents (multi-angle)           | Pass/fail against plan specs               | TDD + fresh subagent review              | Graded scoring rubrics (1-10 per dimension) |
| Agent count         | 12+ specialized agents                          | 5 phases with sub-agents                   | Composable skills (not fixed agents)     | 3 core agents                               |
| Communication       | File-based handoffs between agents              | File-based (CONTEXT.md, PLAN.md, STATE.md) | Subagent dispatch + skill composition    | Agent teams (direct message passing)        |
| Setup complexity    | High (configure 12+ agent personas)             | Medium (installer + 5-phase config)        | Low (plugin install from marketplace)    | Medium (build agents to your needs)         |
| Anthropic alignment | Low -- contradicts high-level planning findings | Medium -- parallel execution aligns        | High -- TDD maps to evaluator concept    | Full -- it IS their findings                |

---

### 3. Decision Wizard

A guided questionnaire that produces a recommendation with full elimination logic. Questions should feel like a conversation with a principal engineer who has used all four approaches.

**Questions (branch based on answers):**

1. **Are you Claude-only or multi-provider?**
   - Claude-only
   - Multi-provider (team uses different AI coding assistants)

2. **What's your primary concern?**
   - Building the wrong thing (vague requirements, wasted implementation)
   - Building it sloppily (skipped tests, inconsistent quality)
   - Losing context across long sessions or multiple sessions
   - Proving what was done and why (compliance, audits, team turnover)

3. **How long do your AI-assisted sessions run?**
   - Short bursts (features, bug fixes -- hours)
   - Extended sessions (6+ hours of continuous work)
   - Multi-session projects spanning days or weeks

4. **Is TDD non-negotiable?**
   - Yes -- need structural enforcement, not encouragement
   - We practice it but don't need guardrails
   - Not a priority

5. **How much setup overhead is acceptable?**
   - Minimal -- install a plugin and go
   - Moderate -- configure some files and personas
   - Heavy is fine if the structure pays off

**Output:** Primary recommendation + optional complementary tool, with:

- Which answers drove the recommendation
- The 2 tradeoffs they're accepting
- Which answers ruled out which options and why

Show the elimination logic visually -- answers should visibly narrow the option space.

---

### 4. Multi-Provider Dilution Visualizer

Three panels showing the three forms of dilution that affect multi-provider frameworks:

**Panel 1: Lowest Common Denominator Prompting**

A Venn diagram showing Claude-specific features, GPT-specific features, and the overlap. Multi-provider frameworks can only use the overlap. Highlight what's lost: Claude's agent teams, model-tuned phrasing, Claude-specific MCP patterns.

Show a table:

| Framework        | Provider-Specific Optimization | What You Lose                                  |
| ---------------- | ------------------------------ | ---------------------------------------------- |
| BMAD             | None -- generic prompts        | Claude-specific features, model-tuned phrasing |
| GSD              | Install paths differ, generic  | Agent teams, Claude-specific MCP patterns      |
| Superpowers      | Claude-first, adding others    | Moving toward generic but still Claude-tuned   |
| Anthropic Native | 100% Claude-optimized          | Nothing                                        |

**Panel 2: Update Lag Timeline**

An animated timeline showing what happens when a new model releases:

- Day 0: Anthropic releases new model. Native harness updated same day.
- Day 1-7: Framework maintainers begin testing.
- Day 7-30: GSD, Superpowers release updates.
- Day 30-90: BMAD, slower frameworks catch up.
- Day ???: Some frameworks never update.

Highlight the "lag window" where you're running new model capabilities through old scaffolding. Include a concrete example: GSD's context isolation was built to solve context anxiety, which Opus 4.6 doesn't exhibit.

**Panel 3: Abstraction Tax**

A layered diagram showing the layers between you and the model. Each framework adds layers. More layers = harder to debug. Quote Anthropic's warning about frameworks creating "extra layers of abstraction that can obscure the underlying prompts and responses."

---

### 5. Obsolescence Risk Matrix

An interactive matrix with a slider from "Current Models" to "Future Models" that shifts the value assessment of each framework component in real-time.

| Component                     | Current Value | Obsolescence Risk | Category                    |
| ----------------------------- | ------------- | ----------------- | --------------------------- |
| BMAD micro-task sharding      | Low           | Already obsolete  | Model limitation workaround |
| BMAD PRD generation           | High          | Low               | Engineering discipline      |
| GSD context isolation         | Low           | Already obsolete  | Model limitation workaround |
| GSD parallel waves            | Medium        | Low               | Orchestration pattern       |
| Superpowers TDD enforcement   | High          | Very low          | Engineering discipline      |
| Superpowers brainstorming     | High          | Very low          | Engineering discipline      |
| Superpowers task breakdown    | Medium        | Medium-High       | Model limitation workaround |
| Anthropic graded evaluation   | High          | Low               | Engineering discipline      |
| Anthropic high-level planning | High          | Very low          | Engineering discipline      |

**Color-code by category:** Engineering discipline = green (durable). Orchestration pattern = blue (stable). Model limitation workaround = red (decaying).

**Surface the pattern explicitly:** Components that encode engineering discipline have low obsolescence risk. Components that encode model limitation workarounds go stale with every model release.

---

### 6. Native vs. Framework Comparison

A two-panel interactive view.

**Left panel: What You Get Natively in Claude Code**

| Capability         | Native Feature                       | Framework Equivalent                  |
| ------------------ | ------------------------------------ | ------------------------------------- |
| Planning           | Plan mode, custom planner agent      | BMAD's multi-agent planning pipeline  |
| Parallel execution | Subagent dispatch (Agent tool)       | GSD's wave executor                   |
| Code review        | Fresh subagent with review prompt    | Superpowers' code review skill        |
| TDD                | Hooks (pre-commit, post-tool-use)    | Superpowers' TDD enforcement          |
| Persistent state   | CLAUDE.md, memory files, MCP servers | GSD's STATE.md + ROADMAP.md           |
| Evaluation         | Custom evaluator agent with rubrics  | All frameworks' verification step     |
| Context management | Automatic compaction                 | GSD's context isolation               |
| Inter-agent comms  | Agent teams (direct message passing) | None -- all frameworks use file-based |

**Right panel: What You Don't Get Natively (framework value-adds)**

| Gap                                          | Best Source | DIY Complexity |
| -------------------------------------------- | ----------- | -------------- |
| Domain-specialized planning agents           | BMAD        | Medium         |
| Wave-based parallel execution with dep graph | GSD         | High           |
| Methodology enforcement (blocks non-TDD)     | Superpowers | Low            |
| Graded scoring rubrics                       | Anthropic   | Low            |
| Socratic brainstorming                       | Superpowers | Low            |
| Multi-session roadmap tracking               | GSD         | Medium         |

Clicking a gap row should expand to show what building it yourself would look like vs. what the framework provides.

---

### 7. Hybrid Builder

An interactive configurator where the user selects components from each framework to compose their own hybrid stack.

**Five slots to fill:**

1. **Planner** -- choose from: Anthropic high-level planning, BMAD analyst/PM agents, GSD research phase, Superpowers brainstorming
2. **Generator** -- choose from: Native Claude Code with subagents, GSD wave executor, Superpowers execute phase
3. **Evaluator** -- choose from: Anthropic graded scoring, Superpowers TDD + review, GSD pass/fail verification, BMAD QA agent
4. **Methodology** -- choose from: Superpowers TDD + brainstorming, Conductor-style gates, none
5. **Orchestration** -- choose from: Native agent teams, GSD wave model, none

**Show conflicts:** If the user picks GSD wave executor + Anthropic agent teams, flag that these are different coordination models.

**Show the recommended hybrid** for a Claude-only user:

- Planner: Anthropic high-level + optionally BMAD analyst for complex projects
- Generator: Native Claude Code with subagent dispatch
- Evaluator: Anthropic graded scoring + Superpowers TDD as complementary
- Methodology: Superpowers brainstorming + TDD (encodes discipline, not model assumptions)
- Orchestration: Native agent teams, GSD waves only if formal dependency graphs needed

---

## Style & UX

- Dark theme -- `#0c0f14` background, cyan/blue accents for interactive elements, red/amber/green for risk/value indicators
- The ablation explorer should animate component removal with visual impact indicators
- Architecture diagrams should be interactive node-and-arrow layouts, not static images
- The obsolescence slider should update the matrix in real-time with smooth transitions
- Decision wizard steps feel progressive -- each answer visibly narrows the option space
- Every recommendation exposes its reasoning -- show which inputs drove which conclusions
- Tooltips on terms: context window, agent teams, wave-based execution, ablation, context anxiety, graded scoring, file-based handoffs
- The dilution timeline should animate as a horizontal sequence

## What This Is Not

- Not a tutorial on installing or configuring any framework
- Not a feature comparison checklist
- Not opinionated about one right answer -- each framework solves different problems
- Not a comparison of coding assistants (Claude Code, Cursor, Copilot) -- those are the layer below
- Not a framework for non-coding agent use cases
