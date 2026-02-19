# LLM Inference Infrastructure

## Input

- [prompt.md](prompt.md)
- [frontend-ecosystem-reference.md](frontend-ecosystem-reference.md)
- [frontend-reference.html](frontend-reference.html)

## Output

- [results.md](results.md)

## Try It Out

Run this from the root directory in order for Claude Code to pick up the [settings](../.claude/settings.json)

```sh
cat <(echo "/playground") frontend-decision-framework/prompt.md \
    <(echo "---\n## Reference Material\n") \
    frontend-decision-framework/frontend-ecosystem-reference.md \
    <(echo "---\n## Existing HTML Reference\n") \
    frontend-decision-framework/frontend-reference.html \
  | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```
