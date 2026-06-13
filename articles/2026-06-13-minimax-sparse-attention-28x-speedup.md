# MiniMax Just Made 1M-Token Context 28× Cheaper — Without Losing a Single Benchmark Point

> MiniMax Sparse Attention (MSA) cuts per-token attention FLOPs by 28.4× at 1M context length on a 109B MoE model while matching full-attention quality across text, math, code, image, video, and agent benchmarks — and the kernel is open-source.

## What Happened

MiniMax released a paper introducing MiniMax Sparse Attention (MSA), a blockwise sparse attention mechanism that achieves a 28.4× reduction in per-token attention FLOPs at 1 million tokens of context — without degrading model quality. Built on top of Grouped Query Attention (GQA), MSA uses an ultra-lightweight Index Branch that scores key-value blocks and selects the top-k for each attention group independently, then runs exact block-sparse softmax attention over only the selected blocks.

The design follows Occam's razor. After extensive ablation, the team retained only the essential components: a max-pooling indexer with a single shared key head across groups, a forced local block for training stability, and a KL-divergence loss that aligns the indexer with the Main Branch attention pattern. The Index Branch adds just two projection matrices to standard GQA.

The paper validates MSA at scale: a 109B-parameter Mixture of Experts model (6B activated per token, 41 layers, 128 routed experts) trained from scratch on 3 trillion tokens with native multimodal data. MSA matched full attention on MMLU (67.2 vs 67.0), outperformed on GSM8K (77.7 vs 76.2) and VisualWebBench (68.4 vs 55.6), and showed negligible PPL gaps on agent benchmarks including SWE-bench, τ²-bench, and Humanity's Last Exam.

## The Architecture

MSA splits attention into two branches. The Index Branch takes stop-gradiented hidden states, projects them through lightweight Q/K matrices (one index key head shared across all GQA groups), and scores blocks via max-pooling over token-level dot products. It selects k=16 blocks of size Bk=128 — a 2,048-token budget per query, fixed regardless of total context length. The local block containing the query is always included.

The Main Branch then runs standard softmax attention over only the selected blocks. Because each GQA group gets its own top-k selection, different groups can attend to different parts of the context — a critical advantage over prior approaches that share a single index across all heads.

The co-designed GPU kernel uses exp-free top-k selection (softmax is order-preserving, so raw scores work), KV-outer iteration for higher arithmetic intensity, query concatenation to fill tensor-core MMAs, and pre-scheduled chunking with two-phase split-K softmax. On H800 GPUs, this delivers 14.2× prefill and 7.6× decoding wall-clock speedups at 1M context.

## Why It Matters

The quadratic cost of softmax attention is the single biggest bottleneck preventing LLMs from operating reliably over ultra-long contexts — the kind needed for agentic workflows that span hundreds of reasoning and action steps, repository-scale code understanding, and persistent memory systems. Every frontier AI company is racing to break this bottleneck.

What makes MSA significant is that it achieves the efficiency of hybrid architectures (linear attention + sparse attention layers) while retaining the quality of pure softmax attention. Prior approaches either replaced attention layers with cheaper alternatives at a quality cost, or used fixed sparse patterns that don't adapt to content. MSA's learned, group-specific indexer makes content-aware decisions about which blocks to attend to, and the results prove it works: a model trained entirely with sparse attention from scratch matches or exceeds full attention across 30+ benchmarks including long-context retrieval (RULER-128K: 77.5 vs 75.0).

The paper also demonstrates a practical conversion path: starting from a pre-trained full-attention checkpoint, continued pretraining with MSA for just 400B tokens (out of 3T total) preserves quality while gaining the efficiency benefits. This means existing model families can adopt MSA without retraining from scratch.

## Developer Impact

The inference kernel is open-source at github.com/MiniMax-AI/MSA, and a production MSA-powered model is already released on Hugging Face as MiniMax-M3. For teams deploying long-context LLMs, this directly translates to lower serving costs and higher throughput.

Two design choices make MSA particularly deployment-friendly. First, the block-granular selection with small k works efficiently across a broad range of GPU architectures — no exotic hardware requirements. Second, the conversion path from dense checkpoints means organizations with existing model investments can adopt MSA incrementally.

The paper signals a broader industry pattern: sparse attention is winning as the path to long-context efficiency. DeepSeek's NSA, MoBA from Moonshot/Kimi, and now MSA from MiniMax all converge on learned, adaptive sparsity over fixed patterns. The question is no longer whether sparse attention works, but whose kernel implementation ships first in production.

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
