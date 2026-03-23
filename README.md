# learning-playgrounds

A collection of interactive learning playgrounds generated with the [Claude Code Playground plugin](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/playground). Each playground is a self-contained HTML explorer built from a prompt, designed to make complex topics hands-on and visual.

## Install the Plugin

From within Claude Code, run:

```
/plugin install playground@claude-plugins-official
```

Or browse for it interactively:

```
/plugin > Discover
```

The plugin is part of the [claude-plugins-official](https://github.com/anthropics/claude-plugins-official) repository maintained by Anthropic.

## Structure

Each topic lives in its own directory:

| File         | Purpose                                             | Required                                        |
| ------------ | --------------------------------------------------- | ----------------------------------------------- |
| `prompt.md`  | The original prompt used to generate the playground | Yes                                             |
| `*.html`     | The generated interactive playground                | No -- prompt-only directories are future builds |
| `results.md` | Summary of what was produced and how to use it      | When HTML exists                                |

## Playgrounds

- **[ai-agent-architecture](ai-agent-architecture/)** -- Interactive concept map covering AI agent patterns: routing, orchestration, delegation, and reflection. Click nodes to explore pseudocode, use cases, and connections between concepts.
- **[llm-inference-infrastructure](llm-inference-infrastructure/)** -- Self-contained HTML playground covering 5 interactive sections: VRAM calculator, quantization tradeoffs, parallelism strategy visualizer, buy vs rent decision tool, and MoE vs dense comparison.
- **[frontend-decision-framework](frontend-decision-framework/)** -- Interactive decision playground for frontend architecture choices: rendering strategy visualizer with timeline controls, meta-framework elimination bracket with constraint filtering, and org structure decision cards.
- **[hosting-decisions](hosting-decisions/)** -- Interactive hosting decision playground with 6 tools: rendering strategy compatibility matrix, cost reality calculator with live bar charts, lock-in exposure visualizer, guided hosting selector wizard, operational complexity spectrum, and Vercel vs Cloudflare comparison.
- **[ai-workflow-orchestrators](ai-workflow-orchestrators/)** -- Interactive decision tool for choosing between AI development workflow orchestrators (GSD, Superpowers, Conductor, Spec Kit). Features a guided questionnaire with ranked recommendations, context management visualizer, flexibility spectrum, model durability timeline, combination recommender, and reconstructability matrix.

### AI Governance (6L-G Framework)

Six playgrounds based on the Six-Level GenAI Governance framework from _AI Governance_ by Bozdag & Bennati (Manning, 2026).

- **[ai-governance-6lg-explorer](ai-governance-6lg-explorer/)** -- Interactive deep-dive into the 6L-G framework. Flowchart navigation through all six governance levels, questions-first detail view with expandable tool pills, deployment model filter (SaaS/API Integrator/Self-Hosted) that reframes questions as vendor due diligence for SaaS customers, and a GDPR erasure walkthrough that traces a deletion request through every level.
- **[ai-governance-assessment](ai-governance-assessment/)** _(prompt only)_ -- Maturity assessment tool. Walk through 24 governance questions scored on a 0-4 maturity scale, get a radar chart of strengths and gaps, and generate a prioritized 30/60/90-day action plan.
- **[ai-governance-risk-profiler](ai-governance-risk-profiler/)** _(prompt only)_ -- Risk profiler for specific AI deployments. Input your use case details (industry, data types, deployment model) and get a risk tier classification, RACI responsibility matrix, and tailored control checklist.
- **[ai-governance-concept-map](ai-governance-concept-map/)** _(prompt only)_ -- Broad concept map covering the full governance landscape: GRC stack (Classic/AI/GenAI layers), deployment models, risk dimensions, case studies, and a connections web showing how everything relates.
- **[ai-governance-scenarios](ai-governance-scenarios/)** _(prompt only)_ -- Scenario-driven explorer with 6 real-world case studies: bank chatbot hallucination, model extraction attack, Snap My AI, GDPR erasure, healthcare deceptive claims, and the DoW/Anthropic contract dispute. Map failures to governance levels and discover cross-scenario patterns.
- **[ai-governance-6lg-reference](ai-governance-6lg-reference/)** _(prompt only)_ -- Reference panel showing how 4 governance challenges (GDPR erasure, prompt injection, model extraction, hiring bias) map across all six levels with tools, owners, and cross-scenario comparison.
