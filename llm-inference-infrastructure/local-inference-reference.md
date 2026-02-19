# Local AI Model Inference Reference

A guide to running open weight models locally, understanding resource requirements, and how quantization works.

## Open Weights Models

"Open weights" refers to AI models where the trained neural network parameters (the weights) are publicly available for download and use, typically under a permissive license.

### What You Get with Open Weights

- Download and run the model locally on your own hardware (e.g., with Ollama)
- Fine-tune the model for specific use cases
- Know exactly what model code is running
- Avoid API dependencies and vendor lock-in
- Keep data on your own infrastructure for privacy

### Common Open Weights Models

- Meta's Llama family (Llama 2, Llama 3)
- Mistral
- Stable Diffusion
- Nous Hermes
- CodeLlama

### Open Weights vs. Alternatives

| Type | Example | Access |
|------|---------|--------|
| Open Weights | Llama, Mistral | Download & run locally |
| Closed Weights | OpenAI GPT models | API only |
| Open Source Code | Can have closed weights | Source visible, model proprietary |

## VRAM Requirements

VRAM (video memory) requirements depend on model size and quantization method. Use these as rough guidelines for single-GPU inference.

### By Model Size

| Model Size | FP32 (Unquantized) | INT4 Quantized | INT8 Quantized | Practical GPU VRAM Needed |
|------------|-------------------|----------------|----------------|--------------------------|
| 3-4B | 6-8GB | 1-2GB | 2-3GB | 6-8GB |
| 7B | ~14GB | 4-8GB | 8GB | 8-12GB |
| 13B | ~26GB | 8-16GB | 13-16GB | 16GB+ |
| 70B | ~140GB | 20-40GB | 35-52GB | 48GB+ or multi-GPU |

### Sweet Spots for Different Setups

- **8GB VRAM**: Run 7B models comfortably (quantized)
- **16GB VRAM**: 13B models comfortably, 7B with higher batch processing
- **24GB+ VRAM**: 70B models, multiple concurrent inferences
- **Consumer GPUs**: RTX 4090 (24GB), RTX 4080 (16GB), RTX 4070 Ti (12GB)
- **Enterprise GPUs**: H100 (80GB), A100 (40-80GB)

### Recommendation for RAG Systems

For RAG over technical documentation (like Broadcom docs), a **7B quantized model on 16GB VRAM** is a solid balance—fast enough for practical use and low operational overhead.

## Quantization

Quantization is the process of reducing the precision of the numbers used to represent a model's weights, trading memory and speed for a small accuracy loss.

### How It Works

Neural networks store learned parameters as numbers. Standard precision is FP32 (32-bit floating point). Quantization converts these to lower-precision formats.

#### Concrete Example

A weight stored as FP32: `0.7364829...`

- **INT8 quantization**: Converts to integer `188` (0-255 range). The model remembers a scaling factor (~0.0039) to convert back to approximately `0.7364`.
- **INT4 quantization**: Converts to integer `12` (0-15 range). Even more aggressive compression with appropriate scaling.

### Common Quantization Methods

| Method | Bits | Size Reduction | Quality | Speed | Use Case |
|--------|------|----------------|---------|-------|----------|
| FP32 | 32 | 1x (baseline) | Best | Slowest | Full precision (rare locally) |
| FP16 | 16 | 2x | Excellent | Fast | GPU native, good balance |
| INT8 | 8 | 4x | Very Good | Very Fast | Good quality, reasonable size |
| INT4 | 4 | 8x | Good | Fastest | Best for local inference |

### Why Quantization Works

During quantization, the model learns the optimal way to map full-precision values to lower-precision ones. This minimizes accuracy loss—the model can often work nearly as well with compressed weights because the precision loss is handled systematically.

### Memory Impact Example

A 70B parameter model:

- **FP32**: ~140GB (impractical for most)
- **INT4**: ~17-20GB (practical on enterprise GPUs)
- **Memory savings**: ~7-8x reduction

### In Practice with Ollama

When you download a model in Ollama:

1. The quantization is already done (pre-quantized weights)
2. You're downloading compressed, quantized weights
3. Inference runs with the quantized model
4. Quality is typically imperceptible for practical tasks

Common Ollama quantization formats: `Q4_K_M` (INT4), `Q5_K_M`, `Q8_0` (INT8)

## Performance Considerations

### Factors Affecting Speed

- **Model size**: Larger models = slower inference
- **Quantization**: Lower precision = faster (INT4 faster than INT8, etc.)
- **Batch size**: Processing multiple inputs together increases throughput
- **GPU type**: GPU architecture matters; some optimize better for certain quantization levels
- **Context length**: Longer input sequences = slower processing (quadratic in transformer models)

### Optimizing for Your Hardware

For typical use cases:
- **Limited VRAM?** Use INT4 quantized 7B models
- **Want quality + speed?** INT4 quantized 13B models
- **Have 24GB+ VRAM?** INT4 quantized 70B models still fit comfortably
- **Need low latency?** Smaller models (3B-7B) with INT4

## References

- [Ollama Documentation](https://github.com/ollama/ollama)
- [Hugging Face Model Hub](https://huggingface.co/models)
- [Llama Models](https://www.llama.com/)
- [Mistral Models](https://mistral.ai/)
