# LLM Inference Infrastructure Reference

A reference for understanding the hardware, memory, and deployment concepts involved in running large language models.

---

## Core Terminology

| Term           | What It Means                                                                                                                                                                                               |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Parameters** | The learned numerical values that define a model's behavior. A "7B model" has 7 billion parameters. More parameters generally means more capability and more memory required.                               |
| **Weights**    | Synonym for parameters. "Full weights" means the complete set of learned values -- the actual model, not a smaller derivative.                                                                              |
| **Tensors**    | Multi-dimensional arrays of numbers. Weights are stored as tensors. A model file is essentially a collection of tensors.                                                                                    |
| **Inference**  | Running a trained model to produce output (answering questions, generating text). Contrast with **training**, which is the process of learning the weights. Inference is cheaper and simpler than training. |
| **Tokens**     | The units a model reads and generates. Roughly ~0.75 words per token in English. A 200K context window means the model can process ~150K words at once.                                                     |

---

## Model Architecture Types

### Dense Models

Every parameter is used for every token. A dense 70B model activates all 70B parameters on every forward pass.

```text
Input → [All 70B parameters active] → Output

Simple but expensive at scale.
```

### Mixture of Experts (MoE)

The model has many "expert" sub-networks but only activates a few per token. This allows a much larger total model with less compute per token.

```text
Input → Router → selects k experts out of N total

Example (GLM-5):
  745B total parameters
  256 experts, 8 active per token
  ~44B parameters active per inference pass
```

**The key tradeoff with MoE:**

|                       | Dense 70B         | MoE 745B (44B active)                              |
| --------------------- | ----------------- | -------------------------------------------------- |
| **Compute per token** | 70B params worth  | 44B params worth (faster)                          |
| **Memory required**   | 70B params loaded | 745B params loaded (all experts must be in memory) |
| **Capability**        | Limited by 70B    | Benefits from 745B total knowledge                 |

MoE saves compute but **not memory**. All experts must reside in VRAM because the router picks different experts for different tokens -- you can't predict which ones you'll need.

---

## Numeric Precision and Quantization

Every parameter is a number stored in some precision format. Lower precision = smaller numbers = less memory, but potentially less accuracy.

### Precision Formats

| Format   | Bits per Parameter | Bytes per Parameter | Description                                                                                                                                                                   |
| -------- | ------------------ | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **FP32** | 32                 | 4                   | Full precision. Used in training, rarely for inference (wasteful).                                                                                                            |
| **FP16** | 16                 | 2                   | Half precision. Good balance of quality and efficiency.                                                                                                                       |
| **BF16** | 16                 | 2                   | "Brain Float 16." Same size as FP16 but trades decimal precision for larger range. Preferred for LLMs because it handles the wide value ranges in transformer weights better. |
| **FP8**  | 8                  | 1                   | Quarter precision. Significant memory savings with modest quality loss. Requires modern GPUs (H100+).                                                                         |
| **INT8** | 8                  | 1                   | 8-bit integer quantization. Similar savings to FP8 with different tradeoffs.                                                                                                  |
| **INT4** | 4                  | 0.5                 | Aggressive quantization. Half the memory of INT8. Noticeable quality degradation on complex reasoning tasks.                                                                  |

### Quantization

The process of converting a model from higher precision to lower precision after training. Think of it as controlled rounding.

```text
Original (BF16):  0.123456789  → stored in 2 bytes
Quantized (INT4): 0.125        → stored in 0.5 bytes

Less precise, but 4x less memory.
```

**Rule of thumb:** INT8/FP8 quantization is usually safe for most use cases. INT4 starts showing quality loss on harder tasks (math, complex reasoning, long outputs).

---

## VRAM: The Bottleneck

**VRAM** (Video RAM) is the memory on a GPU. It's the primary constraint for LLM inference because the entire model must fit in VRAM for fast inference.

### Estimating VRAM Requirements

```text
VRAM ≈ Parameters × Bytes per Parameter + Overhead

Example: 70B model at BF16
  70,000,000,000 × 2 bytes = 140 GB VRAM
  + ~10-20% overhead for KV cache, activations
  ≈ 155-170 GB total
```

### Quick Reference: VRAM by Model Size and Precision

| Model Size       | FP32   | BF16/FP16 | FP8    | INT4   |
| ---------------- | ------ | --------- | ------ | ------ |
| 7B               | 28 GB  | 14 GB     | 7 GB   | 3.5 GB |
| 13B              | 52 GB  | 26 GB     | 13 GB  | 6.5 GB |
| 70B              | 280 GB | 140 GB    | 70 GB  | 35 GB  |
| 405B (Llama 3.1) | 1.6 TB | 810 GB    | 405 GB | 203 GB |
| 745B (GLM-5 MoE) | 3 TB   | 1.5 TB    | 745 GB | 373 GB |

These are base model weights only. Actual deployment needs additional VRAM for the **KV cache** (stored attention state that grows with context length and concurrent users).

### KV Cache

When a model generates text, it stores intermediate attention computations so it doesn't recompute them for every new token. This cache grows with:

- **Context length** -- longer conversations use more cache
- **Concurrent users** -- each user needs their own cache
- **Model dimensions** -- larger models have larger cache entries

A 70B model serving 10 concurrent users with 4K context might need an additional 20-40 GB of VRAM beyond the model weights.

---

## GPU Hardware Reference

### Current Accelerators

| GPU                    | VRAM        | Interconnect     | Notes                                                              |
| ---------------------- | ----------- | ---------------- | ------------------------------------------------------------------ |
| **NVIDIA A100**        | 40 or 80 GB | NVLink 600 GB/s  | Previous generation workhorse. Still widely deployed.              |
| **NVIDIA H100**        | 80 GB       | NVLink 900 GB/s  | Current standard for LLM inference. FP8 support.                   |
| **NVIDIA H200**        | 141 GB      | NVLink 900 GB/s  | Same compute as H100, nearly 2x the VRAM. Better for large models. |
| **NVIDIA B200**        | 192 GB      | NVLink 1800 GB/s | Blackwell generation. 2x NVLink bandwidth over H100.               |
| **AMD MI300X**         | 192 GB      | Infinity Fabric  | AMD's competitor. Large VRAM, growing software support.            |
| **Huawei Ascend 910B** | 64 GB       | HCCS             | China's domestic alternative. GLM-5 was trained on these.          |

### Multi-GPU Systems

| System       | GPUs    | Total VRAM | Use Case                                   |
| ------------ | ------- | ---------- | ------------------------------------------ |
| **DGX H100** | 8× H100 | 640 GB     | Single-node LLM serving up to ~300B at FP8 |
| **DGX H200** | 8× H200 | 1.13 TB    | Single-node for larger models              |
| **DGX B200** | 8× B200 | 1.5 TB     | Single-node for 745B-class models at FP8   |

When a model doesn't fit on one GPU (or one node), you need **parallelism strategies** and fast interconnects.

---

## Parallelism Strategies

When a model is too large for one GPU, you split it across multiple GPUs. How you split it matters.

### Tensor Parallelism (TP)

Splits individual layers across GPUs. Each GPU holds a slice of every layer and they communicate on every token.

```text
Layer 1: [GPU 0 has columns 0-2047] [GPU 1 has columns 2048-4095]
Layer 2: [GPU 0 has columns 0-2047] [GPU 1 has columns 2048-4095]
...

Every token requires GPU-to-GPU communication at every layer.
→ Requires fast interconnect (NVLink), works well within a single node.
```

### Pipeline Parallelism (PP)

Splits the model by layers. GPU 0 gets layers 1-20, GPU 1 gets layers 21-40, etc.

```text
GPU 0: [Layers 1-20] → GPU 1: [Layers 21-40] → GPU 2: [Layers 41-60]

Each token flows through GPUs sequentially.
→ Less communication, but adds latency. Works across nodes.
```

### Data Parallelism (DP)

Each GPU has a full copy of the model. Different requests go to different GPUs.

```text
User A → [Full model on GPU 0]
User B → [Full model on GPU 1]

No communication during inference. Scales throughput linearly.
→ Only works when the model fits on a single GPU.
```

### Typical Deployments

| Model Size | Strategy                     | Example Setup             |
| ---------- | ---------------------------- | ------------------------- |
| 7-13B      | Single GPU                   | 1× H100                   |
| 70B        | TP within one node           | 2-4× H100 (NVLink)        |
| 405B       | TP + PP across nodes         | 2-4 nodes of 8× H100      |
| 745B MoE   | TP + PP + expert parallelism | 2-4 nodes of 8× H100/H200 |

---

## Interconnects

The links between GPUs. Faster interconnects enable more effective parallelism.

| Interconnect   | Bandwidth      | Scope                   | Used For                                                    |
| -------------- | -------------- | ----------------------- | ----------------------------------------------------------- |
| **PCIe Gen5**  | ~128 GB/s      | GPU ↔ CPU               | Baseline. Too slow for tensor parallelism.                  |
| **NVLink**     | 600-1800 GB/s  | GPU ↔ GPU (within node) | Tensor parallelism within a DGX node.                       |
| **NVSwitch**   | Full bisection | All GPUs in node        | Every GPU can talk to every other GPU at full NVLink speed. |
| **InfiniBand** | 400-800 Gb/s   | Node ↔ Node             | Pipeline parallelism across nodes in a cluster.             |

**Why this matters:** Tensor parallelism requires GPUs to exchange data on every token at every layer. If the interconnect is slow, the GPUs spend more time waiting for data than computing. NVLink is fast enough; PCIe is not.

---

## Model Openness Spectrum

"Open source" and "open weights" are not the same thing in AI. The distinction matters for what you can actually do with a model.

| Level                                 | What You Get                                                     | What You Can Do                                                                                                           | Examples                          |
| ------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| **Closed / API-only**                 | Nothing -- just an endpoint                                      | Use it, can't inspect or modify it                                                                                        | GPT-4o, Claude, Gemini            |
| **Open weights**                      | Trained model weights                                            | Download, run locally, fine-tune. But you don't get the training code, data, or full recipe to reproduce it from scratch. | Llama 3, Qwen, Mistral            |
| **Open weights + permissive license** | Weights under MIT, Apache 2.0, etc.                              | Everything above, plus commercial use, redistribution, no usage restrictions                                              | GLM-5 (MIT), Mistral (Apache 2.0) |
| **Open source** (strict definition)   | Weights, training code, data pipeline, and a reproducible recipe | Reproduce the model from scratch, audit the full process                                                                  | OLMo, BLOOM (rare)                |

**Why this matters:** Most models called "open source" are actually **open weights**. You get the finished model but not the training data or the full recipe. That's still useful -- you can run it, fine-tune it, deploy it -- but you can't reproduce or fully audit how it was trained.

Some licenses add usage restrictions on top of open weights. Meta's Llama license, for example, requires a separate agreement for companies with over 700M monthly active users. MIT and Apache 2.0 have no such restrictions.

---

## Model Distribution Formats

How model weights are packaged for download and use.

| Format                 | Description                                                                                                                                  |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **safetensors**        | Standard format for sharing model weights. Fast loading, memory-mapped, no arbitrary code execution risk. Used by HuggingFace.               |
| **GGUF**               | Format optimized for CPU and mixed CPU/GPU inference (llama.cpp). Includes quantized variants. Good for running models on consumer hardware. |
| **PyTorch (.bin/.pt)** | Native PyTorch format. Can contain arbitrary Python code (security risk). Being replaced by safetensors.                                     |
| **ONNX**               | Cross-framework format. Converts models for optimized runtimes (TensorRT, ONNX Runtime).                                                     |

### Where to Get Weights

| Platform        | Description                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------------- |
| **HuggingFace** | Largest model hub. Most open-weight models publish here first.                                                      |
| **ModelScope**  | Chinese equivalent of HuggingFace. Primary distribution for Chinese models (Qwen, GLM).                             |
| **Ollama**      | Simplified local model runner. Downloads and runs models with a single command. Handles quantization automatically. |

---

## Inference Metrics

| Metric                         | What It Measures                         | Good Values                        |
| ------------------------------ | ---------------------------------------- | ---------------------------------- |
| **Tokens/second**              | Generation speed                         | 30-100+ for interactive use        |
| **Time to First Token (TTFT)** | Latency before output starts             | < 1 second for good UX             |
| **Throughput**                 | Total tokens across all concurrent users | Depends on hardware and batch size |
| **Context window**             | Maximum input + output length            | 4K-200K+ tokens depending on model |

### Prefill vs Decode

LLM inference has two phases:

```
[User sends prompt] → PREFILL: Process all input tokens in parallel (compute-bound)
                    → DECODE:  Generate output tokens one at a time (memory-bound)
```

- **Prefill** is fast because all input tokens are processed simultaneously
- **Decode** is slower because each new token depends on the previous one
- Most optimization work focuses on decode speed since that's the bottleneck

---

## Putting It Together: Sizing Example

**Scenario:** Run GLM-5 (745B MoE) for internal use, 10 concurrent users.

```text
1. Model weights at FP8:     745 GB
2. KV cache (10 users, 8K):  ~40 GB
3. Runtime overhead:          ~50 GB
                              --------
   Total VRAM needed:         ~835 GB

4. Hardware options:
   - 2× DGX H200 nodes (8× 141 GB = 1.13 TB each) → comfortable headroom
   - 2× DGX H100 nodes (8× 80 GB = 640 GB each, 1.28 TB total) → tight but workable
   - 1× DGX B200 node (8× 192 GB = 1.5 TB) → single node, simplest ops

5. Networking:
   - NVLink within each node (tensor parallelism)
   - InfiniBand between nodes (pipeline parallelism)

6. Ballpark cost (cloud):
   - H100 spot instances: ~$2-3/GPU/hour × 16 GPUs ≈ $35-50/hour
   - H100 on-demand: ~$4-5/GPU/hour × 16 GPUs ≈ $65-80/hour
   - Owned hardware: $300K-600K capital per DGX node
```

---

## Buy vs. Rent: The Hardware Investment Question

Models change every few months. Does it make sense to buy GPU hardware?

### The Financial Breakeven

```text
2× DGX H200 nodes:          ~$800K-$1M capital
Equivalent cloud (16× H200): ~$50-80K/month = $600K-$960K/year
Breakeven at 24/7 usage:     ~12-18 months
```

After breakeven, you're running at the cost of power, cooling, and staff. But 24/7 utilization is the key assumption -- most companies overestimate their actual inference volume.

### Why the Hardware Doesn't Become Useless

VRAM and compute are fungible. As models get more efficient, owned hardware goes from "barely fits one model" to "can serve multiple models with headroom." The hardware becomes overprovisioned, not obsolete.

The efficiency trend is real: a 70B model from next year may match a 745B model from today. That means your 2-node cluster that was stretched thin can now serve the same quality from a single node, freeing the other for a different workload.

### When to Buy

| Signal                               | Why                                                                       |
| ------------------------------------ | ------------------------------------------------------------------------- |
| **Regulatory constraint**            | Defense (ITAR), healthcare, finance -- on-prem is mandatory, not a choice |
| **Consistent high-volume inference** | Millions of requests/day where utilization stays high                     |
| **Continuous fine-tuning**           | Training and inference on the same hardware amortizes better              |
| **Data can't leave the building**    | Sovereign data requirements that cloud regions don't satisfy              |

### When to Rent

| Signal                           | Why                                                                                |
| -------------------------------- | ---------------------------------------------------------------------------------- |
| **Variable or uncertain demand** | Pay-per-use beats idle hardware                                                    |
| **No dedicated ML infra team**   | Running a GPU cluster requires 1-2 FTEs at $200-350K/yr each                       |
| **Rapid experimentation**        | Trying different models weekly doesn't justify owning hardware for any one of them |
| **Startup or early-stage**       | Capital is better spent elsewhere until inference volume justifies it              |

### The Middle Ground: Reserved Instances

Most companies land here. Cloud providers offer 1-3 year reserved instance commitments at 40-60% discounts over on-demand pricing. You lock in capacity and cost without the capital outlay, operational burden, or depreciation risk of owned hardware.

### The Hidden Costs of Owning

```text
Hardware:        $800K-$1M (2× DGX H200)
Data center:     Power, cooling, rack space, networking
Staff:           1-2 ML infra engineers ($200-350K/yr each)
Maintenance:     Hardware failures, firmware updates, driver compatibility
Opportunity:     Capital locked in depreciating hardware vs. invested elsewhere
```

### The Hidden Costs of Renting

```text
Hourly rates:    Add up fast at high utilization ($600K-$960K/yr for 16 GPUs)
Availability:    GPU capacity can be scarce during demand spikes
Egress fees:     Moving large models and datasets in/out of cloud costs money
Vendor lock-in:  Provider-specific tooling (SageMaker, Vertex) creates switching costs
```
