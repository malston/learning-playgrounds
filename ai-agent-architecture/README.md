# AI Agent Architecture

## Input

See [prompt.md](prompt.md)

## Output

See [results.md](results.md)

## Try It Out

```sh
cat <(echo "/playground") prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```

## Full Transcript

```text
✻
    |
   ▟█▙     Claude Code v2.1.45
 ▐▛███▜▌   Opus 4.6 · Claude Max
▝▜█████▛▘  ~/code/learning-playgrounds/ai-agent-architecture
  ▘▘ ▝▝
  ⎿  SessionStart:startup says: [ai-agent-architecture] recent context, 2026-02-18 9:38am MST
     ────────────────────────────────────────────────────────────

     Legend: session-request | 🔴 bugfix | 🟣 feature | 🔄 refactor | ✅ change | 🔵 discovery | ⚖️ decision

     Column Key
       Read: Tokens to read this observation (cost to learn it now)
       Work: Tokens spent on work that produced this record ( research, building, deciding)

     Context Index: This semantic index (titles, types, files, tokens) is usually sufficient to understand past work.

     When you need implementation details, rationale, or debugging context:
       - Use MCP tools (search, get_observations) to fetch full observations on-demand
       - Critical types ( bugfix, decision) often need detailed fetching
       - Trust this index over re-reading code for past decisions and learnings

     Context Economics
       Loading: 3 observations (1,520 tokens to read)
       Work investment: 7,016 tokens spent on research, building, and decisions
       Your savings: 5,496 tokens (78% reduction from reuse)

❯ /playground
  # Prompt

  Teach me how AI agents actually work. Not the buzzwords. The architecture. How routing, orchestration, and delegation actually fit together. For example:

  - What makes an agent different from a chatbot
  - 4 core patterns (routing, orchestration, delegation, reflection)
  - When to use single vs multi-agent systems
  - How each pattern maps to actual code


⏺ Skill(playground)
  ⎿  Successfully loaded skill

⏺ Using the playground skill to build an interactive concept map explorer for AI agent architecture.

  Let me load the concept-map template first.

⏺ Searched for 1 pattern, read 2 files (ctrl+o to expand)

⏺ I see there was a previous version of this playground. Let me check what currently exists.

⏺ Searched for 1 pattern (ctrl+o to expand)

⏺ Bash(git log --oneline -5)
  ⎿  46a1f71 Fix the command
     463b057 Remove generated content                                                                                                                                                       ccb6451 Restructure content
     … +2 lines (ctrl+o to expand)

⏺ The previous HTML was removed. I'll build a fresh one from scratch, incorporating improvements. Let me also handle the uncommitted changes first per the version control rules.

⏺ Bash(git status)
  ⎿  On branch main
     Your branch is up to date with 'origin/main'.

     nothing to commit, working tree clean

⏺ Clean tree, good. Now let me build the playground. This is a concept-map style interactive explorer -- I'll follow the template closely.

⏺ Write(ai-agent-architecture.html)
  ⎿  Wrote 858 lines to ai-agent-architecture.html
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
     … +848 lines (ctrl+o to expand)

⏺ Bash(open /Users/markalston/code/learning-playgrounds/ai-agent-architecture/ai-agent-architecture.html)
  ⎿  (No output)

⏺ The playground is open in your browser, Mark. Here's what you've got:

  Interactive concept map with 15 nodes across 5 categories:
  - Foundation: LLM → Chatbot → Agent (the progression from raw model to autonomous agent)
  - Components: Tool Use, Memory, Planning (what agents are made of)
  - Patterns: Routing, Orchestration, Delegation, Reflection (the 4 architectural patterns)
  - Systems: Single-Agent vs Multi-Agent (when to use which)
  - Roles: Supervisor, Worker, Evaluator (who does what in multi-agent systems)

  How to use it:
  - Click any node to see its description, real pseudocode, when to use it, and its connections
  - Click the colored dot on a node to cycle knowledge level (Know / Fuzzy / Unknown)
  - Drag nodes to rearrange the map to match your mental model
  - Use presets at the top ("I'm new", "I know chatbots", etc.) to quickly set knowledge levels
  - Copy the prompt at the bottom -- it builds a personalized learning request based on your knowledge gaps

  Each node includes concrete implementation code showing how the pattern actually works, not just theory.

✻ Crunched for 3m 34s

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
❯ open it in the browser so I can see it
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  [Opus 4.6 | Max] │ ai-agent-architecture git:(main*)                                                                                                                 ⧉ In README.md
  Context ███░░░░░░░ 31% │ Usage █░░░░░░░░░ 13% (1h 19m / 5h)
  ⏵⏵ bypass permissions on (shift+tab to cycle) · 2 files +74 -53```
```
