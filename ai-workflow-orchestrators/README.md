# Hosting Decisions

## Input

- [prompt.md](prompt.md)

## Output

- [results.md](results.md)

## Try It Out

Run this from the root directory in order for Claude Code to pick up the [settings](../.claude/settings.json)

```sh
cat <(echo "/playground") ai-workflow-orchestrators/prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```
