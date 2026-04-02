# AI Governance 6L-G Explorer

## Input

See [prompt.md](prompt.md)

## Output

See [results.md](results.md)

## Try It Out

Run this from the root directory in order for Claude Code to pick up the [settings](../.claude/settings.json)

```sh
cat <(echo "/playground") ai-governance-6lg-explorer/prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```
