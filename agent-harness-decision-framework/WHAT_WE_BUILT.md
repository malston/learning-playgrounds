# Agent Harness Decision Framework -- What We Built

A single-file interactive playground for senior engineers deciding whether to adopt a third-party agent framework (BMAD, GSD, Superpowers), build on Anthropic's native harness design, or compose a hybrid. Based on Anthropic's ablation experiments with Opus 4.6 and direct analysis of each framework's architecture.

The core question: **does a third-party harness add value over what the model can do natively, or does it encode stale assumptions that slow you down?**

## How to Use

Open `index.html` in a browser. No build step, no dependencies, no server required.

The playground has 7 tabs. Each one approaches the decision from a different angle. Work through whichever tabs match your situation -- you don't need to use all of them. The prompt output at the bottom updates live based on your selections and can be copied into Claude for follow-up.

## The 7 Tabs

### 1. Ablation Explorer

Toggle harness components on and off to see what Opus 4.6 actually needs vs what Sonnet 4.5 needs. Each component shows its impact when removed, color-coded green (safe to drop), amber (moderate), or red (significant loss).

Three presets: Full Harness, Minimal (planner + generator + evaluator), Bare Generator.

The takeaway: most scaffolding was built for weaker models. The surviving core is planner, generator, evaluator -- everything else is optional.

### 2. Architecture

Side-by-side node diagrams for all four approaches. Click any node to see what it does, what data flows in and out, and how it communicates. A comparison table below covers provider targeting, planning depth, evaluation method, agent count, communication style, and setup complexity.

### 3. Decision Wizard

Answer 5 questions (provider strategy, primary concern, session length, TDD stance, setup tolerance). Each answer visibly narrows the option space in the elimination tracker on the right. The final recommendation includes which answers drove it, which frameworks were eliminated and why, and the tradeoffs you're accepting.

### 4. Dilution

Three panels showing why multi-provider support degrades framework quality:

- **Lowest common denominator** -- Venn diagram of what's lost when frameworks target the overlap of model capabilities
- **Update lag timeline** -- what happens between a model release and when frameworks catch up
- **Abstraction tax** -- layer diagram showing what sits between your prompt and the model

### 5. Obsolescence

A slider from "current models" to "future models" that shifts the value and risk assessment of each framework component in real-time. Components are color-coded by category: engineering discipline (green, durable), orchestration pattern (blue, stable), model limitation workaround (red, decaying).

### 6. Native vs Framework

Two-panel comparison. Left: what Claude Code provides natively (plan mode, subagent dispatch, hooks, memory, agent teams). Right: genuine framework value-adds with DIY complexity ratings. Click any gap row to expand and see what building it yourself looks like vs what the framework provides.

### 7. Hybrid Builder

Pick components from each approach across 5 slots: planner, generator, evaluator, methodology, orchestration. The builder flags conflicts (e.g., mixing GSD's file-based waves with native agent teams). Four presets: Recommended (Claude-only), Max Structure, Lightweight, Clear All.

## Prompt Output

At the bottom of every tab, a prompt updates live based on your selections. It generates contextual natural language -- not a value dump -- that you can copy and paste into Claude for further discussion. The prompt includes only non-default choices and enough context to be actionable without the playground open.

## Technical Details

- Single HTML file, ~1,800 lines, inline CSS and JS
- No external dependencies or CDN links
- Dark theme with cyan/blue interactive accents, red/amber/green for risk indicators
- ARIA roles on tabs (keyboard arrow navigation), labeled toggle switches, accessible prompt toggle
- State managed through a single `state` object; all controls write to it, all renders read from it
- Error boundary on init catches failures with a visible diagnostic instead of a blank page
