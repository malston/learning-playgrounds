# AI Agent Architecture

## Input

See [prompt.md](prompt.md)

## Output

See [results.md](results.md)

## Try It Out

CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions --chrome -p < prompt.md

## Full Transcript

```text
❯ /playground:playground Teach me how AI agents actually work.
Not the buzzwords. The architecture. How routing, orchestration, and delegation actually fit together.
For example:
- What makes an agent different from a chatbot
- 4 core patterns (routing, orchestration, delegation, reflection)
- When to use single vs multi-agent systems
- How each pattern maps to actual code

⏺ I'll load the concept-map template since this is a learning/exploration playground.

⏺ Read 1 file (ctrl+o to expand)

⏺ Using the concept-map template to build an interactive AI agent architecture explorer.
⎿  API Error: Claude's response exceeded the 32000 output token maximum. To configure this behavior, set the CLAUDE_CODE_MAX_OUTPUT_TOKENS environment variable.

⏺ Let me build the playground now.

⏺ Write(ai-agent-architecture.html)
⎿  Wrote 608 lines to ai-agent-architecture.html
      1 <!DOCTYPE html>
      2 <html lang="en">
      3 <head>
      4 <meta charset="UTF-8">
      5 <meta name="viewport" content="width=device-width, initial-scale=1.0">
      6 <title>AI Agent Architecture Explorer</title>
      7 <style>
      8 *{margin:0;padding:0;box-sizing:border-box}
      9 :root{
    10   --bg:#0d1117;--surface:#161b22;--border:#30363d;
    … +598 lines (ctrl+o to expand)

⏺ Bash(open /Users/markalston/code/technical-cheatsheets/ai-agent-architecture.html)
⎿  (No output)

⏺ The playground is open in your browser, Mark. Here's what you've got:

Interactive concept map with 15 nodes across 5 categories:
- Foundation: LLM, Chatbot, Agent
- Components: Tool Use, Memory, Planning
- Patterns: Routing, Orchestration, Delegation, Reflection
- Systems: Single-Agent, Multi-Agent
- Roles: Supervisor, Worker, Evaluator

How to use it:
- Click any node to see its description, pseudocode implementation, and when to use it
- Click the colored dot on a node (or use the detail panel buttons) to cycle knowledge level: Know / Fuzzy / Unknown
- Drag nodes to rearrange the map to match your mental model
- Use presets to quickly set knowledge levels ("I'm new", "I know chatbots", etc.)
- Copy the prompt at the bottom -- it generates a personalized learning request based on what you know vs. don't know

Each pattern includes real pseudocode showing the actual code structure, not just theory.

✻ Cooked for 11m 40s
```
