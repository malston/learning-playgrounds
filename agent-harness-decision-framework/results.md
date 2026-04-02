# Agent Harness Decision Framework

Choosing between third-party agent frameworks (BMAD, GSD, Superpowers) and building on Anthropic's native harness design. Based on Anthropic's ablation experiments with Opus 4.6 and direct analysis of each framework's architecture, provider strategy, and update velocity.

The core question: **does a third-party harness add value over what the model can do natively, or does it encode stale assumptions that slow you down?**

---

## The Principle That Drives Everything

Every component in an agent harness encodes an assumption about what the model cannot do on its own. These assumptions go stale as models improve. Anthropic validated this by systematically removing harness components and measuring the impact on Opus 4.6.

Their findings:

| Component Removed                           | Impact on Opus 4.6                                         | Impact on Sonnet 4.5                           |
| ------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| Sprint decomposition                        | None -- model maintained coherence without task breakdown  | Significant -- lost coherence on long tasks    |
| Context resets                              | None -- no "context anxiety" observed                      | Significant -- premature task completion       |
| Detailed task specs                         | Negative -- micro-specs caused cascading errors when wrong | Positive -- model needed step-by-step guidance |
| Sprint contract (gen/eval negotiate "done") | Minimal -- generator handled quality on its own            | Moderate -- evaluator had to catch more gaps   |
| Evaluator agent                             | Significant for subjective tasks (UI, UX)                  | Significant across all task types              |
| Planner agent                               | Significant -- generators under-scoped without it          | Significant                                    |

**What survived the ablation: planner, generator, evaluator.** Everything else is optional scaffolding whose value depends on model capability.

---

## Framework Comparison

### Architecture at a Glance

```text
BMAD (Documentation-First)              GSD (Spec-Driven)

┌──────────┐                             ┌──────────────┐
│ Analyst  │──► PRD                      │ Orchestrator │
├──────────┤                             └──────┬───────┘
│ Architect│──► Architecture Doc          ┌─────┴──────┐
├──────────┤                              ▼            ▼
│ PM       │──► User Stories        ┌──────────┐ ┌──────────┐
├──────────┤                        │Researcher│ │ Planner  │
│ Developer│──► Implementation      │(4 agents)│ │+ Checker │
├──────────┤                        └──────────┘ └────┬─────┘
│ QA       │──► Code Review                           ▼
└──────────┘                              ┌───────────────────┐
12+ agents, heavy docs                    │ Parallel Executors │
                                          │ (wave-based)       │
                                          └─────────┬─────────┘
Superpowers (Skills-Based)                          ▼
                                          ┌───────────────────┐
┌──────────────┐                          │    Verifier        │
│  Brainstorm  │                          └───────────────────┘
│  (Socratic)  │                          5 phases, context isolation
└──────┬───────┘
       ▼                                 Anthropic Native
┌──────────────┐
│  Plan (2-5   │                         ┌──────────┐
│  min tasks)  │                         │ Planner  │──► product-level spec
└──────┬───────┘                         └────┬─────┘
       ▼                                      ▼
┌──────────────┐                         ┌──────────┐  ┌───────────┐
│  Execute     │                         │Generator │◄►│ Evaluator │
│  (subagents) │                         │          │  │ (graded   │
└──────┬───────┘                         │          │  │  scoring) │
       ▼                                 └──────────┘  └───────────┘
┌──────────────┐                         3 agents, agent teams
│  TDD + Code  │                         (agents communicate directly)
│  Review      │
└──────────────┘
7 stages, skill composition
```

### Detailed Comparison

| Dimension               | BMAD                                              | GSD                                        | Superpowers                              | Anthropic Native                            |
| ----------------------- | ------------------------------------------------- | ------------------------------------------ | ---------------------------------------- | ------------------------------------------- |
| **Provider target**     | Agnostic (Claude, GPT, Gemini, Grok)              | Agnostic (9+ runtimes)                     | Claude-first, multi-provider added       | Claude-only                                 |
| **Core value prop**     | Specialized domain agents for requirements        | Context isolation + parallel waves         | TDD enforcement + methodology discipline | Graded evaluation + minimal scaffolding     |
| **Planning depth**      | Heavy: PRD → architecture → stories → micro-tasks | Medium: research → atomic XML plans        | Medium: brainstorm → 2-5 min tasks       | Light: product-level spec only              |
| **Evaluation method**   | Code review + QA agents (multi-angle)             | Pass/fail against plan specs               | TDD + fresh subagent review              | Graded scoring rubrics (1-10 per dimension) |
| **Agent count**         | 12+ specialized agents                            | 5 phases with sub-agents                   | Composable skills (not fixed agents)     | 3 core agents                               |
| **Communication**       | File-based handoffs between agents                | File-based (CONTEXT.md, PLAN.md, STATE.md) | Subagent dispatch + skill composition    | Agent teams (direct message passing)        |
| **Setup complexity**    | High (configure 12+ agent personas)               | Medium (installer + 5-phase config)        | Low (plugin install from marketplace)    | Medium (build agents to your needs)         |
| **Anthropic alignment** | Low -- contradicts high-level planning findings   | Medium -- parallel execution aligns        | High -- TDD maps to evaluator concept    | Full -- it IS their findings                |

---

## The Multi-Provider Dilution Problem

When a framework targets multiple AI providers, every prompt must work across models with different strengths, context handling, and instruction-following characteristics. This creates three forms of dilution:

### 1. Lowest Common Denominator Prompting

Prompts that work on Claude AND Gemini AND GPT cannot use features unique to any one model. Claude's agent teams (direct inter-agent communication) are unavailable if your prompts also need to work on Gemini CLI, which uses a different coordination model.

| Framework            | Provider-Specific Optimization                                | What You Lose                                  |
| -------------------- | ------------------------------------------------------------- | ---------------------------------------------- |
| **BMAD**             | None -- generic structured prompts                            | Claude-specific features, model-tuned phrasing |
| **GSD**              | Installation paths differ, prompts are generic                | Agent teams, Claude-specific MCP patterns      |
| **Superpowers**      | Acknowledges provider differences (e.g., Codex "too literal") | Moving toward generic but still Claude-tuned   |
| **Anthropic Native** | 100% Claude-optimized                                         | Nothing -- full access to all Claude features  |

### 2. Update Lag

Model capabilities advance on Anthropic's release schedule. Frameworks update on their maintainers' schedules. The gap between these creates a period where your harness encodes stale assumptions.

```text
Timeline of a model release:

Day 0:  Anthropic releases Opus 4.6
        ├── Anthropic's harness design: updated same day (same team)
        ├── Sprint decomposition: now unnecessary
        └── Context anxiety: eliminated

Day 1-7:   Framework maintainers begin testing
Day 7-30:  GSD, Superpowers release updates
Day 30-90: BMAD, slower-moving frameworks catch up
Day ???:   Some frameworks never update (abandoned)

During the lag window, you're running new model capabilities
through old scaffolding designed for weaker models.
```

**Concrete example:** GSD's core innovation is context isolation -- spawning fresh 200K context windows per task to avoid context anxiety. Opus 4.6 doesn't exhibit context anxiety. The isolation still _works_, but it adds orchestration overhead for a problem that no longer exists.

### 3. Abstraction Tax

Multi-provider frameworks add abstraction layers that obscure what's happening between you and the model. Anthropic explicitly warns about this: frameworks "create extra layers of abstraction that can obscure the underlying prompts and responses, making them harder to debug."

---

## What Each Framework Gets Right (Despite the Concerns)

No framework is all dead weight. Each contributes something that Anthropic's minimal harness doesn't address:

| Framework       | Genuine Value-Add                                       | Why It Matters                                                                                             |
| --------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **BMAD**        | PRD generation with specialized domain agents           | Anthropic's planner is generic; BMAD's analyst/PM agents have domain-specific context baked in             |
| **GSD**         | Parallel wave execution with dependency graphs          | Anthropic's harness is serial; GSD's wave model handles independent tasks concurrently                     |
| **Superpowers** | TDD enforcement and methodology discipline              | Anthropic relies on the evaluator catching problems; Superpowers prevents them with test-first development |
| **Superpowers** | Brainstorming phase (Socratic questioning)              | Catches edge cases and requirements gaps before planning begins                                            |
| **GSD**         | Persistent state across sessions (STATE.md, ROADMAP.md) | Anthropic's harness assumes single long sessions; GSD handles multi-session projects                       |

---

## Decision Tree

```text
What is your situation?
│
├─► Single provider (Claude only)?
│         │
│         YES
│         │
│         ├─► Building a long-running app (hours of continuous work)?
│         │         │
│         │         YES ──► Anthropic Native Harness
│         │                 Build planner + generator + evaluator agents
│         │                 using Claude Agent SDK or agent teams.
│         │                 Add graded scoring rubrics for subjective tasks.
│         │
│         ├─► Need strong requirements/planning discipline?
│         │         │
│         │         YES ──► BMAD (PRD phase only) + Anthropic Native
│         │                 Use BMAD's analyst/PM agents for PRD generation.
│         │                 Skip everything after PRD -- use Anthropic's
│         │                 high-level planning for implementation.
│         │
│         ├─► Need strict development methodology (TDD, code review)?
│         │         │
│         │         YES ──► Superpowers
│         │                 Best alignment with Anthropic's findings.
│         │                 Claude-first design minimizes dilution.
│         │                 TDD enforcement complements the evaluator concept.
│         │
│         └─► Multi-session project with dependency tracking?
│                   │
│                   YES ──► GSD or your own orchestration
│                           GSD's wave execution and persistent state
│                           handle this well. Alternatively, build
│                           custom orchestration with beads/pvg.
│
├─► Multi-provider requirement?
│         │
│         YES
│         │
│         ├─► Teams standardizing across providers?
│         │         │
│         │         YES ──► GSD
│         │                 Best multi-runtime support (9+ runtimes).
│         │                 Consistent workflow regardless of provider.
│         │
│         └─► Heavy upfront planning needed?
│                   │
│                   YES ──► BMAD
│                           12+ specialized agents for thorough
│                           requirements and architecture documentation.
│
└─► Not sure / exploring?
          │
          └──► Start with Anthropic's native approach.
               Add framework components only when you hit a gap.
               This avoids encoding assumptions you can't validate.
```

---

## The "Build Native" Argument

For Claude-only users, building on Anthropic's native mechanisms has structural advantages that no third-party framework can match:

### What You Get Natively in Claude Code (no framework needed)

| Capability         | Native Feature                                | Framework Equivalent                           |
| ------------------ | --------------------------------------------- | ---------------------------------------------- |
| Planning           | Plan mode, custom planner agent               | BMAD's multi-agent planning pipeline           |
| Parallel execution | Subagent dispatch (Agent tool)                | GSD's wave executor                            |
| Code review        | Fresh subagent with review prompt             | Superpowers' code review skill                 |
| TDD                | Hooks (pre-commit, post-tool-use)             | Superpowers' TDD enforcement                   |
| Persistent state   | CLAUDE.md, memory files, MCP servers          | GSD's STATE.md + ROADMAP.md                    |
| Evaluation         | Custom evaluator agent with rubrics           | All frameworks' verification step              |
| Context management | Automatic compaction (no anxiety on Opus 4.6) | GSD's context isolation                        |
| Inter-agent comms  | Agent teams (direct message passing)          | None -- all frameworks use file-based handoffs |

### What You Don't Get Natively (framework value-adds)

| Gap                                                  | Best Framework Source      | DIY Complexity                       |
| ---------------------------------------------------- | -------------------------- | ------------------------------------ |
| Domain-specialized planning agents                   | BMAD                       | Medium -- write custom agent prompts |
| Wave-based parallel execution with dependency graphs | GSD                        | High -- build dependency resolver    |
| Methodology enforcement (blocks non-TDD code)        | Superpowers                | Low -- hooks can enforce this        |
| Graded scoring rubrics                               | Anthropic's harness design | Low -- add to evaluator prompt       |
| Socratic brainstorming                               | Superpowers                | Low -- write a brainstorming skill   |
| Multi-session roadmap tracking                       | GSD                        | Medium -- use beads or custom state  |

### The Hybrid Sweet Spot

The strongest setup for a Claude-only user is not "pick one framework" but "build native, borrow selectively":

1. **Planner**: Anthropic's high-level, product-focused approach. Optionally use BMAD's analyst/PM agents for PRD generation on complex projects.
2. **Generator**: Native Claude Code with subagent dispatch. No framework needed.
3. **Evaluator**: Anthropic's graded scoring approach. Borrow Superpowers' TDD discipline as a complementary mechanism (tests catch regressions; scored evaluation catches quality drift).
4. **Methodology**: Superpowers' brainstorming and TDD skills. These encode discipline, not model assumptions, so they don't go stale.
5. **Orchestration**: Native agent teams for inter-agent communication. GSD's wave model only if you need formal dependency graphs.

---

## Obsolescence Risk Assessment

How likely is each framework's core value to become unnecessary as models continue improving?

| Framework Component                | Current Value                                 | Obsolescence Risk    | Reasoning                                                                              |
| ---------------------------------- | --------------------------------------------- | -------------------- | -------------------------------------------------------------------------------------- |
| BMAD micro-task sharding           | Low (already counterproductive per Anthropic) | **Already obsolete** | Opus 4.6 handles high-level plans without breakdown                                    |
| BMAD PRD generation                | High                                          | Low                  | Domain expertise in prompts doesn't go stale with model improvements                   |
| GSD context isolation              | Low (solves eliminated problem)               | **Already obsolete** | Opus 4.6 has no context anxiety                                                        |
| GSD parallel waves                 | Medium                                        | Low                  | Parallelism is an orchestration pattern, not a model-capability assumption             |
| GSD spec-driven verification       | Medium                                        | Medium               | Better models need less external verification                                          |
| Superpowers TDD enforcement        | High                                          | Very low             | TDD is engineering discipline, not a model limitation workaround                       |
| Superpowers brainstorming          | High                                          | Very low             | Socratic questioning catches human blind spots, not model blind spots                  |
| Superpowers 2-5 min task breakdown | Medium                                        | Medium-High          | Same issue as micro-tasks -- Anthropic says unnecessary for Opus 4.6                   |
| Anthropic graded evaluation        | High                                          | Low                  | Subjective quality assessment will need external evaluation for the foreseeable future |
| Anthropic high-level planning      | High                                          | Very low             | Product-level thinking is the planner's job regardless of model capability             |

**Pattern**: Components that encode _engineering discipline_ (TDD, brainstorming, evaluation rubrics) have low obsolescence risk. Components that encode _model limitation workarounds_ (context isolation, micro-task breakdown, sprint contracts) go stale with every model release.

---

## Quick Reference: When to Use What

| Scenario                                           | Recommended Approach                                 | Reasoning                                                                                |
| -------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Solo developer, Claude-only, greenfield app        | Anthropic native + Superpowers TDD                   | Minimal overhead, maximum model leverage                                                 |
| Team standardizing across AI providers             | GSD                                                  | Best multi-runtime support, consistent workflow                                          |
| Complex enterprise project with heavy requirements | BMAD (PRD phase) + Anthropic native (implementation) | BMAD's domain agents excel at requirements; skip its implementation scaffolding          |
| Existing Superpowers user                          | Keep it, strip micro-task breakdown                  | Already Claude-first with low dilution; its TDD and brainstorming are genuinely valuable |
| Long-running autonomous build (6+ hours)           | Anthropic native harness with graded evaluator       | Designed exactly for this scenario; agent teams enable direct gen/eval communication     |
| Multi-session project with dependencies            | GSD or custom orchestration (beads/pvg)              | GSD's persistent state and wave execution handle this well                               |
| Rapid prototyping / exploration                    | No framework -- raw Claude Code                      | Frameworks add overhead for exploratory work; use plan mode and iterate                  |

---

## Sources

- [Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps) -- Anthropic's ablation experiments
- [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) -- Graded evaluation methodology
- [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system) -- Orchestrator-worker patterns
- [Building effective agents](https://www.anthropic.com/research/building-effective-agents) -- Foundational agent patterns
- [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) -- Documentation-first agile AI development
- [GSD (Get Shit Done)](https://github.com/gsd-build/get-shit-done) -- Spec-driven development with context isolation
- [Superpowers](https://github.com/obra/superpowers) -- Skills-based development methodology
- [Superpowers 5 release notes](https://blog.fsck.com/2026/03/09/superpowers-5/) -- Evolution with model capabilities
