# LLM Inference Infrastructure Playground

Build an interactive learning playground that teaches the core concepts of LLM inference infrastructure — hardware, memory, quantization, parallelism, and deployment decisions — through hands-on exploration.

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

Output: total VRAM needed (weights + KV cache + overhead), hardware recommendations from the GPU reference table, and whether it fits on a single node or requires multi-node parallelism.

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

Platform engineers and infrastructure architects who are new to ML infrastructure but comfortable with systems concepts (memory, networking, distributed systems). They understand what a GPU is but have never had to size one for an LLM workload.
