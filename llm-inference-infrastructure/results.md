# Results

5 interactive sections, all in a single HTML file (no dependencies):

1. VRAM Calculator -- Select model/precision/users/context, see a stacked bar of weights + KV cache + overhead with full formula breakdown and GPU hardware
   recommendations
2. Quantization Explorer -- Side-by-side vertical bars for FP32/FP16/FP8/INT4 with quality/speed indicators, plus a byte-level visualization showing how the value
   0.7834 degrades across precisions
3. Parallelism Visualizer -- Visual diagram of GPU arrangements for TP/PP/DP with auto-recommendation logic and explanations of communication requirements (NVLink vs
   InfiniBand)
4. Buy vs Rent -- Cost comparison across on-demand, 1yr reserved, 3yr reserved, and owned hardware with breakeven analysis and compliance toggle
5. MoE vs Dense -- Side-by-side 70B dense vs 745B MoE comparison with expert grid visualization, router explanation, and tradeoff table

**Features:**

- 4 presets (Starter/Team/Production/Enterprise) that snap all controls
- Tooltips on key terms (KV cache, tensor parallelism, MoE, etc.)
- Each section has a copyable natural-language summary
- All math is shown, not hidden
- Dark theme
