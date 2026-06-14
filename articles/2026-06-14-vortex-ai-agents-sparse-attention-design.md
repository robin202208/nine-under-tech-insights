# AI Agents Are Now Designing Their Own Inference Optimizations — And They're Better Than Ours

**Date:** 2026-06-14  
**Category:** AI Infrastructure · Sparse Attention · Agent-Driven Research  
**Source:** CMU / Rice University / NUS (arXiv: 2606.06453)

---

## What Happened

A team from Carnegie Mellon, Rice, and the National University of Singapore has built **Vortex**, a system that lets AI agents autonomously design, deploy, and optimize sparse attention algorithms — and the agents are producing results that match or beat hand-crafted approaches.

Here's the problem Vortex solves: sparse attention is increasingly essential for long-context LLM inference. Agentic workflows, repository-scale reasoning, and reinforcement learning all demand attention over hundreds of thousands of tokens. But deploying a *new* sparse attention algorithm in a real serving system (like SGLang or vLLM) is brutally engineering-intensive. Implementing just one variant — Double Sparse — took roughly 2,000 lines of C++/CUDA in SGLang, much of it reimplementing basic operators over paged tensor layouts.

Vortex collapses this to a few lines of Python. It provides **vFlow**, a Python-embedded DSL where programmers specify *what* sparsity pattern to apply and *how* attention is computed, without touching memory layouts. Underneath, **vTensor** — a page-centric tensor abstraction — handles the paged-KV-cache reality of modern serving systems transparently.

The researchers then gave Claude Code (Opus 4.7, Sonnet 4.6) and OpenAI Codex (GPT-5.5) access to Vortex along with 8 reference papers, and asked them to generate 20 novel sparse attention algorithms each. All 60 generated algorithms compiled and ran on the first try. Claude Sonnet 4.6 produced the most structurally diverse designs, with algorithms hitting 2×–3.1× higher throughput than full attention while matching dense-attention accuracy on RULER, AMC23, and AIME24.

Then they let Claude Code run an 18-hour autonomous optimization loop targeting AIME24 on Qwen3-1.7B. The result: a sparse attention flow achieving **3.46× higher throughput** (11,894 vs. 3,437 tok/s) while actually *improving* accuracy (38.96 vs. 38.54 mean@16), all on an NVIDIA H200.

## Why It Matters

Sparse attention research has been stuck in a painful bottleneck: ideas are cheap, but validation is expensive. Quest's original implementation was 44.4× *slower* than SGLang's full attention, taking nearly two hours to evaluate a single benchmark. Researchers couldn't quickly test whether a new sparsity pattern actually delivered end-to-end speedups.

Vortex breaks this bottleneck by making sparse attention *programmable and composable*. The key insight is decomposing sparse attention into two stages: a query-independent cache construction phase (precompute block summaries, centroid vectors) and a query-dependent indexer phase (score blocks, select top-k, run attention). Both are expressed through a small set of reusable operators — GeMM, Top-K, Gather, Mean — that Vortex compiles into efficient GPU kernels with automatic fusion.

The AI agent results are the headline, but the deeper story is what Vortex enables for *any* attention architecture. The team extended it to multi-head latent attention (MLA), the compressed-KV design used by DeepSeek and GLM. On GLM-4.7-Flash running on an NVIDIA B200, a rope-aware block-sparse flow they designed in vFlow achieved **4.7× higher throughput** with no accuracy loss on AIME26. On the 229B-parameter MiniMax-M2.7 across four B200 GPUs, block top-k achieved 1.37× speedup.

## The Bigger Picture

This is more than a systems paper. It's a proof point that AI agents can now function as co-researchers in AI infrastructure design — not just generating plausible code, but producing *Pareto-efficient* algorithms that survive rigorous accuracy-throughput benchmarking.

The feedback loop is obvious and self-reinforcing: better AI models design better serving infrastructure, which enables longer-context, higher-throughput inference, which enables better AI models. The 18-hour autonomous loop (23 iterations, 92 submissions) that beat hand-tuned baselines suggests we're at the beginning of a curve where AI-driven systems optimization becomes not just competitive but superior.

The numbers also tell a practical story about the economics of serving. A 3.46× throughput improvement on the same hardware means the same GPU fleet can serve 3.46× more requests — or you can serve the same load with 71% fewer GPUs. When deployed at hyperscaler scale, that's hundreds of millions of dollars in infrastructure savings, found by an AI agent running overnight.

---

*Full paper: [Vortex: Efficient and Programmable Sparse Attention Serving for AI Agents](https://arxiv.org/abs/2606.06453)*
*GitHub: [Infini-AI-Lab/vortex_torch](https://github.com/Infini-AI-Lab/vortex_torch)*
