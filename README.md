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

Each topic lives in its own directory with three files:

| File         | Purpose                                             |
| ------------ | --------------------------------------------------- |
| `prompt.md`  | The original prompt used to generate the playground |
| `*.html`     | The generated interactive playground                |
| `results.md` | Summary of what was produced and how to use it      |

## Playgrounds

- **[ai-agent-architecture](ai-agent-architecture/)** -- Interactive concept map covering AI agent patterns: routing, orchestration, delegation, and reflection. Click nodes to explore pseudocode, use cases, and connections between concepts.
- **[llm-inference-infrastructure](llm-inference-infrastructure/)** -- Self-contained HTML playground covering 5 interactive sections: VRAM calculator, quantization tradeoffs, parallelism strategy visualizer, buy vs rent decision tool, and MoE vs dense comparison. Live math formulas, GPU hardware recommendations, and copyable configuration summaries for each section. 4 presets (Starter/Team/Production/Enterprise) and inline tooltips on key terms.
