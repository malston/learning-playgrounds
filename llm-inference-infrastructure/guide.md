# LLM Inference Infrastructure -- A Practical Guide

This guide explains the concepts behind the [LLM Inference Infrastructure Playground](llm-inference-playground.html) and how to use each of its five interactive sections. The playground is a single-file HTML tool with no dependencies -- just open it in a browser.

## Quick Start

Open the playground and click a **preset** at the top to load a scenario:

| Preset     | Scenario                                     | Model | Precision |
| ---------- | -------------------------------------------- | ----- | --------- |
| Starter    | Single developer experimenting locally       | 7B    | FP16      |
| Team       | Small team with moderate traffic             | 13B   | FP16      |
| Production | High-traffic production service              | 70B   | FP8       |
| Enterprise | Large-scale, compliance-sensitive deployment | 405B  | FP16      |

Presets snap every control across all five sections, giving you a coherent starting point. Adjust individual controls to explore "what if" scenarios.

---

## Key Terms

These terms appear throughout the playground. Each is also available as a hover tooltip in the HTML.

### Memory and Storage

- **VRAM** -- Video RAM. The high-bandwidth memory on a GPU. Every model, KV cache entry, and runtime buffer must fit in VRAM during inference.
- **KV Cache** -- During text generation, the model stores the attention keys (K) and values (V) it has already computed for previous tokens. This avoids re-computing them for each new token, but the cache grows linearly with sequence length and concurrent users.
- **Overhead** -- Additional VRAM consumed by the CUDA runtime context, intermediate activation tensors, and framework buffers. The playground estimates this at 15% of the combined weight and KV cache memory.

### Precision and Quantization

- **Precision** -- The numerical format used to store model weights. More bits means higher fidelity, but also more memory and slower computation.
  - **FP32** (32-bit float) -- Full precision. Baseline for comparison, rarely used for inference.
  - **FP16 / BF16** (16-bit float) -- Half precision. Negligible quality loss. The standard for most inference deployments.
  - **FP8** (8-bit float) -- Quarter precision. Minimal quality loss on most tasks. Increasingly supported on modern GPUs (H100, B200).
  - **INT4** (4-bit integer) -- Aggressive quantization. Noticeable degradation on complex reasoning. Useful when VRAM is severely constrained.
- **Quantization** -- The process of converting model weights from a higher precision (e.g., FP32) to a lower one (e.g., INT4). Reduces memory usage and increases throughput, with some accuracy tradeoff.
- **Sign / Exponent / Mantissa** -- The three components of a floating-point number. Sign determines positive/negative, exponent sets the magnitude range, and mantissa (also called significand) holds the fractional precision. Fewer bits in each component reduces the range and precision of representable values.

### Parallelism

- **Tensor Parallelism (TP)** -- Splits each layer's weight matrices across multiple GPUs. All GPUs compute a partial result for every layer, then combine results via an all-reduce operation. Requires high-bandwidth intra-node interconnects like NVLink (~900 GB/s on H100).
- **Pipeline Parallelism (PP)** -- Assigns different groups of layers to different GPUs (or nodes). Data flows through GPUs in sequence. Requires less interconnect bandwidth than TP but introduces "pipeline bubbles" -- periods where some GPUs idle while waiting for upstream stages to finish (typically 10-20% overhead).
- **Data Parallelism (DP)** -- Runs complete copies of the model on separate GPU sets. Each copy handles different requests independently. Multiplies throughput linearly with no inter-GPU communication during inference.
- **NVLink** -- A high-bandwidth interconnect within a single server node. H100 NVLink provides ~900 GB/s between GPUs. Required for efficient tensor parallelism.
- **InfiniBand** -- A high-speed network interconnect between server nodes. Provides ~50 GB/s (~400 Gb/s). Used for pipeline parallelism across nodes.
- **Node** -- A single server, typically containing 8 GPUs connected by NVLink. Multiple nodes are connected via InfiniBand.

### Model Architecture

- **Dense Model** -- A standard neural network where every parameter participates in computing every token. Simple and well-understood, but compute cost scales linearly with parameter count.
- **Mixture of Experts (MoE)** -- An architecture that contains many parallel "expert" sub-networks within each layer. A learned router selects a small subset of experts (e.g., 2 out of 128) for each token. This gives a model much more total capacity without proportionally increasing compute cost.
- **Router** -- The learned gating mechanism in MoE models that scores all experts for a given token and selects the top-K to activate.
- **Active Parameters** -- In an MoE model, the parameters that actually compute for a given token. For a 745B MoE model with 2 of 128 experts active, only ~52B parameters compute per token.

### Cost Terms

- **On-Demand** -- Pay-per-hour cloud GPU pricing with no commitment. Most flexible, most expensive per hour.
- **Reserved Instance** -- Committing to continuous usage for 1 or 3 years in exchange for lower per-hour rates. The GPU is allocated whether you use it or not.
- **Own Hardware** -- Purchasing GPUs and operating them in your own data center. High upfront cost, lowest long-term per-hour cost at scale.
- **Breakeven** -- The usage threshold (hours/day) at which one pricing model becomes cheaper than another.

---

## Section 1: VRAM Calculator

### What it does

Calculates the total GPU memory required to serve a specific model configuration, broken down into three components:

1. **Model weights** -- `parameters x bytes_per_parameter`
2. **KV cache** -- `2 x layers x kv_heads x head_dim x bytes x context_length x concurrent_users`
3. **Overhead** -- 15% of the above for CUDA context, activations, and framework buffers

### Controls

| Control          | What it changes                                               |
| ---------------- | ------------------------------------------------------------- |
| Model Size       | Parameter count (7B through 745B MoE)                         |
| Precision        | Bytes per parameter (FP32=4, FP16=2, FP8=1, INT4=0.5)         |
| Concurrent Users | Number of simultaneous sessions, each with their own KV cache |
| Context Window   | Token length per session (2K through 128K)                    |

### What you see

- **Total VRAM** -- The headline number in GB
- **Stacked bar** -- Visual breakdown of weights vs. KV cache vs. overhead
- **Formula breakdown** -- Every step of the calculation shown explicitly
- **Hardware table** -- Which GPUs can fit the model (single GPU, multi-GPU, multi-node)

### Why it matters

VRAM is the hard constraint for LLM deployment. If the model does not fit, it does not run. This section helps you answer:

- Can my model fit on a single GPU, or do I need multiple?
- How much does increasing the context window cost in memory?
- What happens to memory when I scale from 10 to 100 concurrent users?
- Is the memory dominated by weights or by KV cache at my scale?

### Try this

Set the model to 70B, precision to FP8, context to 32K, and slide concurrent users from 1 to 200. Watch the KV cache grow from a small fraction to the dominant memory consumer.

---

## Section 2: Quantization Trade-off Explorer

### What it does

Compares the four precision levels side-by-side for a given model size, showing how each one trades memory for quality and speed.

### Controls

| Control    | What it changes                                             |
| ---------- | ----------------------------------------------------------- |
| Model Size | Which model's memory footprint to compare across precisions |

### What you see

- **Vertical bars** -- Memory usage at each precision (FP32, FP16, FP8, INT4) with percentage reduction
- **Speed multiplier** -- Relative inference speed gain (1x through 5x)
- **Quality badges** -- Baseline, Safe, or Caution rating for each precision
- **Bit-level visualization** -- Shows how the number 0.7834 is encoded in each format, how many bits are available for sign/exponent/mantissa, and the resulting approximation error

### Why it matters

Quantization is the single highest-leverage optimization for LLM inference. Going from FP16 to FP8 halves memory and roughly doubles throughput with minimal quality loss. This section helps you understand:

- Exactly how much memory each precision level saves
- The concrete tradeoff: what does "4.3% error per weight" actually look like?
- Why INT4 quantization uses FP16 for KV cache (activations need higher precision than stored weights)
- At what precision level quality degradation becomes noticeable

### Try this

Select the 405B model. Notice that FP32 requires 1.5 TB (impossible on current GPUs), while INT4 brings it down to ~202 GB (3 H100s). The bit visualization shows you exactly where that 8.7% per-weight error comes from.

---

## Section 3: Parallelism Strategy Visualizer

### What it does

Given a model, precision, and GPU count, recommends and visualizes the optimal parallelism strategy with a diagram showing how layers and weight shards are distributed across GPUs.

### Controls

| Control    | What it changes                              |
| ---------- | -------------------------------------------- |
| Model Size | Determines weight footprint and layer count  |
| Precision  | Affects how many GPUs the model needs to fit |
| Total GPUs | How many H100 80GB GPUs are available        |

### What you see

- **GPU diagram** -- Color-coded boxes showing which shard or layer group each GPU holds
- **Communication labels** -- What type of interconnect is needed (NVLink for TP, InfiniBand for PP)
- **Strategy recommendation** -- Why the specific combination of TP/PP/DP was chosen
- **Insufficient GPU warning** -- If you select fewer GPUs than the model requires

### The recommendation logic

1. If the model fits on 1 GPU and you have 1 GPU: no parallelism needed
2. If the model fits on 1 GPU and you have multiple: use data parallelism (copies for throughput)
3. If the model needs 2-8 GPUs: use tensor parallelism within a node
4. If the model needs more than 8 GPUs: combine tensor + pipeline parallelism across nodes
5. Any remaining GPUs are used for data parallelism (replicas)

### Why it matters

Parallelism decisions directly affect latency, throughput, and hardware requirements. Wrong choices waste GPUs or create bottlenecks. This section helps you understand:

- Why you cannot simply "add more GPUs" to make a model faster (TP communication overhead)
- When you need to cross node boundaries (PP) and what that costs in pipeline bubbles
- How data parallelism provides linear throughput scaling without communication overhead
- The difference between GPUs needed to _fit_ a model vs. GPUs needed to _serve_ at target throughput

### Try this

Set the model to 405B at FP16, and increase GPUs from 8 to 16 to 32 to 64. Watch the strategy evolve from pure tensor parallelism to tensor + pipeline, and see data parallelism replicas appear as you add GPUs beyond what the model needs.

---

## Section 4: Buy vs. Rent Decision Tool

### What it does

Estimates the total GPU count needed for a workload and compares monthly costs across four infrastructure options: on-demand cloud, 1-year reserved, 3-year reserved, and owned hardware.

### Controls

| Control             | What it changes                                                |
| ------------------- | -------------------------------------------------------------- |
| Requests / Day      | Daily inference request volume (100 to 1M)                     |
| Model Size          | Determines throughput per GPU and GPUs per serving instance    |
| Usage Hours / Day   | How many hours the service runs daily (affects on-demand cost) |
| Compliance Required | Forces the recommendation to on-prem regardless of cost        |

### What you see

- **Infrastructure sizing** -- GPUs per instance, throughput, demand calculation, total GPUs needed
- **Cost comparison table** -- Monthly and annual costs for all four options with "Best" recommendation
- **Breakeven analysis** -- At what daily usage hours on-demand becomes more expensive than reserved

### Assumptions

- All cost calculations assume FP8 precision and H100 80GB GPUs
- Throughput estimates assume continuous batching at 75% utilization
- Average request size is 1000 tokens (500 input + 500 output)
- Own hardware capital cost is amortized over 3 years
- Cloud pricing represents ballpark averages across major providers

### Why it matters

GPU infrastructure is expensive. The difference between on-demand and 3-year reserved can be 2x. Owned hardware can be cheaper still at scale, but requires significant upfront investment. This section helps you answer:

- How many GPUs do I actually need for my traffic volume?
- At what scale does owning hardware break even with cloud?
- How much does reducing usage hours (e.g., off-peak shutdown) save on on-demand?
- When does compliance force the on-prem decision regardless of cost?

### Try this

Set model to 70B, requests to 50,000/day, and slide usage hours from 24 down to 8. Watch on-demand become competitive with reserved instances as daily usage drops.

---

## Section 5: MoE vs. Dense Comparison

### What it does

Places a 70B dense model side-by-side with a 745B MoE (Mixture of Experts) model to illustrate the fundamental tradeoff: MoE models have far more total parameters but activate only a small fraction per token.

### Controls

| Control   | What it changes                          |
| --------- | ---------------------------------------- |
| Precision | Affects memory footprint for both models |

### What you see

- **Side-by-side stats** -- Total parameters, active parameters per token, memory, and GPU requirements for each architecture
- **Expert grid** -- Visual grid showing which experts are active (2 of 128) for a given token
- **Router visualization** -- How tokens flow through each architecture
- **Tradeoff table** -- Direct comparison of compute, memory, quality, and hardware requirements

### Key insight

The 745B MoE model has 10x the parameters of the 70B dense model, but activates fewer parameters per token (52B vs 70B). This means:

- **Less compute** per token (26% less despite 10x more total parameters)
- **More memory** required (all 128 experts must be loaded even though only 2 are used)
- **Better quality-per-FLOP** because experts can specialize

### Why it matters

MoE is the architecture behind models like DeepSeek and Mixtral. Understanding the compute/memory tradeoff is essential for:

- Capacity planning (MoE needs more VRAM but less compute per token)
- Hardware selection (MoE benefits from memory bandwidth more than raw compute)
- Understanding why some very large models are surprisingly fast at inference
- Evaluating the "bigger is better" assumption -- MoE shows you can have massive capacity without proportional compute cost

### Try this

Toggle precision between FP16 and INT4. Notice that the MoE model's memory goes from ~1,490 GB to ~372 GB -- still requiring multiple GPUs, but the savings make it practical on fewer nodes.

---

## Putting It All Together

A typical workflow through the playground:

1. **Start with VRAM** -- Pick your model and precision. How much memory do you need?
2. **Explore quantization** -- Can you drop to FP8 or INT4 to fit on fewer GPUs?
3. **Plan parallelism** -- Given your GPU count, what strategy works?
4. **Estimate cost** -- At your traffic level, should you rent or buy?
5. **Consider MoE** -- Would a larger MoE model give better quality at similar or lower compute cost?

Each section has a **Copy** button on its summary text. These natural-language summaries are designed to be pasted into architecture documents, Slack messages, or planning proposals.
