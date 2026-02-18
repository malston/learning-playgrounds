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

     Feb 18, 2026

     ai-agent-architecture.html
       #22614  9:29 AM  🟣  Interactive AI Agent Architecture Explorer Built
         A comprehensive interactive learning tool was built to teach AI agent architecture through visual concept mapping. The explorer presents core agent patterns
     (routing, orchestration, delegation, reflection) alongside foundational concepts (LLM, chatbot vs agent distinctions) and architectural choices (single vs
     multi-agent systems). The interface uses an interactive node-link diagram where learners can track their understanding level for each concept, rearrange nodes to
     match their mental models, and generate personalized learning prompts based on knowledge gaps. This transforms abstract agent architecture concepts into concrete,
      explorable patterns with actual pseudocode examples mapped to implementation approaches.
         (~444t) (🛠️ 1,116t)


     prompt.md
       #22615           🔵  AI Agent Architecture Learning Playground Project Structure Analyzed
         The AI Agent Architecture learning playground is a previous effort to create an interactive tool for teaching agent architecture concepts. It consists of a
     prompt defining learning objectives (routing, orchestration, delegation patterns; agent vs chatbot distinctions; single vs multi-agent systems; code mapping), a
     results file documenting what was built, and a README with full transcript. The core deliverable was a 608-line HTML interactive concept map with 15 nodes
     spanning foundational concepts, components, patterns, and architectural systems. The tool enables learners to track their understanding level for each concept,
     rearrange nodes visually, view pseudocode implementations, and generate personalized learning paths based on knowledge gaps. The project demonstrates a "learning
     by exploration" approach using an interactive concept map rather than linear documentation.
         (~500t) (🔍 1,068t)

       #22616  9:30 AM  🔵  Conversation History Shows Interactive Playground Already Generated
         The conversation archive reveals that an interactive concept map explorer was previously built to teach AI agent architecture. The artifact is a 608-line HTML
      file with embedded styling and JavaScript that provides interactive learning through a visual concept map with 15 nodes spanning Foundation (LLM, Chatbot,
     Agent), Components (Tool Use, Memory, Planning), Patterns (Routing, Orchestration, Delegation, Reflection), Systems (Single-Agent, Multi-Agent), and Roles
     (Supervisor, Worker, Evaluator). The interface allows learners to click nodes for details and pseudocode, track their knowledge level for each concept, rearrange
     node positions to build personal mental models, use preset learning configurations, and generate personalized learning prompts based on knowledge gaps. The
     generation required working around Claude's output token limits by increasing the CLAUDE_CODE_MAX_OUTPUT_TOKENS configuration. The current session is determining
     whether this interactive playground meets the learning goal or if additional formats (written walkthrough, runnable code examples) would be more valuable.
         (~576t) (🔍 4,832t)



     Access 7k tokens of past research & decisions for just 1,520t. Use MCP search tools to access memories by ID.

     View Observations Live @ http://localhost:37777












❯ /playground
  # Prompt

  Teach me how AI agents actually work. Not the buzzwords. The architecture. How routing, orchestration, and delegation actually fit together. For example:

  - What makes an agent different from a chatbot
  - 4 core patterns (routing, orchestration, delegation, reflection)
  - When to use single vs multi-agent systems                                                                                                                            - How each pattern maps to actual code
                                                                                                                                                                       ⏺ Skill(playground)
  ⎿  Successfully loaded skill

⏺ Using the playground skill to build an interactive concept map explorer for AI agent architecture.

  Let me load the concept-map template first.

⏺ Searched for 1 pattern, read 2 files (ctrl+o to expand)

⏺ I see there was a previous version of this playground. Let me check what currently exists.

⏺ Searched for 1 pattern (ctrl+o to expand)

⏺ Bash(git log --oneline -5)
  ⎿  46a1f71 Fix the command
     463b057 Remove generated content
     ccb6451 Restructure content
     … +2 lines (ctrl+o to expand)

⏺ The previous HTML was removed. I'll build a fresh one from scratch, incorporating improvements. Let me also handle the uncommitted changes first per the version
  control rules.

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

❯ /exit
  ⎿  See ya!

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
❯ 
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  [Opus 4.6 | Max] │ ai-agent-architecture git:(main*)                                                                                                 ⧉ In README.md
  Context ███░░░░░░░ 31% │ Usage █░░░░░░░░░ 13% (1h 19m / 5h)
  ⏵⏵ bypass permissions on (shift+tab to cycle) · 2 files +3 -0

Resume this session with:
claude --resume c98a1d9a-c627-4d7c-9728-54941ac45f80
☸ cluster01 (kube-system) learning-playgrounds/ai-agent-architecture 🌱 main [!?][⏱ 39m13s]
❯ cd ../llm-inference-infrastructure
☸ cluster01 (kube-system) learning-playgrounds/llm-inference-infrastructure 🌱 main [!?]
❯ cat <(echo "/playground") prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions

╭─── Claude Code v2.1.45 ─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│                                      │ Tips for getting started                                                                                                     │
│          Welcome back Mark!          │ Run /init to create a CLAUDE.md file with instructions for Claude                                                            │
│                                      │ ─────────────────────────────────────────────────────────────────                                                            │
│                                      │ Recent activity                                                                                                              │
│                                      │ No recent activity                                                                                                           │
│                   ✻                  │                                                                                                                              │
│                   |                  │                                                                                                                              │
│                  ▟█▙                 │                                                                                                                              │
│                ▐▛███▜▌               │                                                                                                                              │
│               ▝▜█████▛▘              │                                                                                                                              │
│                 ▘▘ ▝▝                │                                                                                                                              │
│        Opus 4.6 · Claude Max         │                                                                                                                              │
│   ~/…/llm-inference-infrastructure   │                                                                                                                              │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
  ⎿  SessionStart:startup says: [llm-inference-infrastructure] recent context, 2026-02-18 10:17am MST
     ────────────────────────────────────────────────────────────

     No previous sessions found for this project yet.

     View Observations Live @ http://localhost:37777

❯ /playground
  # LLM Inference Infrastructure Playground

  Build an interactive learning playground that teaches the core concepts of LLM inference infrastructure — hardware, memory, quantization, parallelism, and
  deployment decisions — through hands-on exploration.

  ## Learning Goals

  By the end, the learner should be able to:

  - Estimate VRAM requirements for any model size and precision
  - Understand why quantization works and when it hurts quality
  - Explain the difference between tensor, pipeline, and data parallelism — and when to use each
  - Navigate the buy vs. rent vs. reserved instance decision
  - Understand MoE vs. dense model tradeoffs

  ## Core Interactive Elements

  ### 1. VRAM Calculator

  An interactive calculator where the user selects:

  - Model size (7B, 13B, 70B, 405B, 745B MoE)
  - Precision format (FP32, BF16/FP16, FP8, INT4)
  - Number of concurrent users
  - Context window size

  Output: total VRAM needed (weights + KV cache + overhead), hardware recommendations from the GPU reference table, and whether it fits on a single node or
  requires multi-node parallelism.

  ### 2. Quantization Trade-off Explorer

  Visual slider or selector that shows:

  - Memory footprint at each precision level for a chosen model
  - Quality impact (Safe / Caution / Noticeable degradation)
  - Speed impact
  - Concrete byte-level example showing how a weight value changes from FP32 → INT4

  ### 3. Parallelism Strategy Visualizer

  An animated or interactive diagram showing how a model is split across GPUs for:

  - Tensor Parallelism (layers split horizontally across GPUs)
  - Pipeline Parallelism (layers split vertically, GPUs in sequence)
  - Data Parallelism (full model copies, different requests)

  User selects a model size and GPU count, and the playground recommends the right strategy with a visual explanation of why.

  ### 4. Buy vs. Rent Decision Tool

  Interactive decision tree / calculator:

  - Input: estimated inference volume (requests/day), model size, compliance requirements
  - Output: recommended path (buy / reserved / on-demand) with cost comparison
  - Show the breakeven math dynamically

  ### 5. MoE vs. Dense Comparison

  Side-by-side visualization of a dense 70B model vs. a MoE 745B model showing:

  - Active parameters per token (compute cost)
  - Total parameters loaded (memory cost)
  - How the router selects experts

  ## Style & UX

  - Dark theme, clean and modern
  - Sections flow top to bottom but each is self-contained
  - Key terms have inline tooltips on hover (e.g., hover "KV cache" → short definition)
  - No quizzes — this is a reference tool for practitioners, not a course
  - Show the math, don't hide it — engineers want to see the formulas

  ## Audience

  Platform engineers and infrastructure architects who are new to ML infrastructure but comfortable with systems concepts (memory, networking, distributed
  systems). They understand what a GPU is but have never had to size one for an LLM workload.


⏺ Skill(playground)
  ⎿  Successfully loaded skill

⏺ Using the playground skill to build this. Let me first check the project state and load the template.

⏺ Bash(git status && echo "---" && ls -la)
  ⎿  On branch main
     Your branch is up to date with 'origin/main'.
     … +16 lines (ctrl+o to expand)

⏺ Searched for 1 pattern, read 2 files (ctrl+o to expand)

⏺ Mark, before I start building -- a quick note on git. You've got uncommitted changes in ../ai-agent-architecture/ (modified prompt.md and untracked
  agent-architecture-prompt.md). Those are in a sibling project so they won't interfere, but you may want to commit them separately. I'll create a feature branch for
  this work.

⏺ Bash(cd /Users/markalston/code/learning-playgrounds && git checkout -b feature/llm-inference-playground)
  ⎿  Switched to a new branch 'feature/llm-inference-playground'
  ⎿  Shell cwd was reset to /Users/markalston/code/learning-playgrounds/llm-inference-infrastructure

⏺ Now let me build the playground. This is a substantial single-file HTML application with 5 interactive sections.

✳ Accomplishing… (10m 28s · ↓ 36.7k tokens · thought for 68s)

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
❯ 
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  [Opus 4.6 | Max] │ llm-inference-infrastructure git:(feature/llm-inference-playground)                                                               ⧉ In prompt.md
  Context ████░░░░░░ 39% │ Usage ██░░░░░░░░ 15% (33m / 5h)
  ⏵⏵ bypass permissions on (shift+tab to cycle)
```
