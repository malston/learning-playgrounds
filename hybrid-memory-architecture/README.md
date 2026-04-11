# Hybrid Memory Architecture

Interactive playground exploring a four-layer memory architecture that teaches AI coding agents to remember across sessions, compactions, and projects.

## Layers

| Layer           | Purpose                          | Persistence            |
| --------------- | -------------------------------- | ---------------------- |
| Beads           | Work tracking with dependencies  | Dolt database          |
| Auto-Memory     | User preferences and corrections | Markdown files         |
| Claude-Mem      | Past actions and outcomes        | SQLite via PostToolUse |
| Episodic Memory | Conversation history             | Indexed transcripts    |

## Sections

- **Architecture** -- Layer overview, session data flow, five kinds of forgetting
- **Memory Router** -- Routing table and interactive quiz
- **Claude-Mem vs Episodic** -- Side-by-side comparison and decision helper
- **Session Lifecycle** -- Timeline with token budget breakdown

## Input

See [prompt.md](prompt.md)

## Output

See [results.md](results.md)

## Try It Out

Run this from the root directory in order for Claude Code to pick up the [settings](../.claude/settings.json)

```sh
cat <(echo "/playground") hybrid-memory-architecture/prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```
