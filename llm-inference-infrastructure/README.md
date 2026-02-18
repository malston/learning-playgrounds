# LLM Inference Infrastructure

## Input

See [prompt.md](prompt.md)

## Output

- [results.md](results.md)
- [llm-inference-playground.html](llm-inference-playground.html)

## Try It Out

```sh
cat <(echo "/playground") prompt.md | CLAUDE_CODE_MAX_OUTPUT_TOKENS=64000 claude --dangerously-skip-permissions
```

## Full Transcript

> Note: This session was initiated from the `ai-agent-architecture` directory
> and the startup context reflects that working directory.

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

⏺ Write(llm-inference-playground.html)
  ⎿  Wrote 1532 lines to llm-inference-playground.html
       1 <!DOCTYPE html>
       2 <html lang="en">
       3 <head>
       4 <meta charset="UTF-8">
       5 <meta name="viewport" content="width=device-width, initial-scale=1.0">
       6 <title>LLM Inference Infrastructure Playground</title>
       7 <style>
       8 :root {
       9   --bg-page: #08080e;
      10   --bg-section: #0e0e1a;
     … +1522 lines (ctrl+o to expand)

⏺ Bash(open /Users/markalston/code/learning-playgrounds/llm-inference-infrastructure/llm-inference-playground.html)
  ⎿  (No output)

⏺ The playground is open in your browser. Here's what I built:

  5 interactive sections, all in a single HTML file (no dependencies):

  1. VRAM Calculator -- Select model/precision/users/context, see a stacked bar of weights + KV cache + overhead with full formula breakdown and GPU hardware
  recommendations
  2. Quantization Explorer -- Side-by-side vertical bars for FP32/FP16/FP8/INT4 with quality/speed indicators, plus a byte-level visualization showing how the value
  0.7834 degrades across precisions
  3. Parallelism Visualizer -- Visual diagram of GPU arrangements for TP/PP/DP with auto-recommendation logic and explanations of communication requirements (NVLink vs
   InfiniBand)
  4. Buy vs Rent -- Cost comparison across on-demand, 1yr reserved, 3yr reserved, and owned hardware with breakeven analysis and compliance toggle
  5. MoE vs Dense -- Side-by-side 70B dense vs 745B MoE comparison with expert grid visualization, router explanation, and tradeoff table

  Features:
  - 4 presets (Starter/Team/Production/Enterprise) that snap all controls
  - Tooltips on key terms (KV cache, tensor parallelism, MoE, etc.)
  - Each section has a copyable natural-language summary
  - All math is shown, not hidden
  - Dark theme

  Take a look and let me know what you'd like adjusted. I'm on the feature/llm-inference-playground branch.

✻ Cogitated for 15m 43s
```
