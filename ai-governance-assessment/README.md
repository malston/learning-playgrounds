# AI Governance Assessment

## Input

See [prompt.md](prompt.md)

## Try It Out

Run this from the root directory in order for Claude Code to pick up the [settings](../.claude/settings.json)

```sh
cat <(echo "/playground") ai-governance-assessment/prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```
