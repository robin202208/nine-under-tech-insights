# MiniMax Just Open-Sourced an Agent Model That Runs 24 Hours Straight — And Beats GPT-5.5 at Coding

> China's Xiyu Technology released MiniMax M3 on June 1: a fully open-source model combining top-tier coding, 1M-context, and native multimodal capabilities. It autonomously reproduced an ICLR paper in 12 hours, ran for 24 hours straight with 2,000+ tool calls, and beats GPT-5.5 on SWE-Bench Pro. The architecture powering this is entirely novel.

## What Happened

On June 1, 2026, MiniMax (Xiyu Technology) released M3 — the first fully open-source Chinese large model to combine three capabilities that previously existed only in separate, mostly closed-source systems: top-tier software engineering performance, a 1-million-token context window, and native multimodal input including images, video, and desktop operation.

The benchmarks are aggressive. M3 surpasses GPT-5.5 and Gemini 3.1 Pro on SWE-Bench Pro, the authoritative software engineering evaluation. But the numbers only tell half the story. What distinguishes M3 is its behavior under extended autonomous operation — the kind of real-world agent workloads that break most models.

In one demonstration, M3 autonomously reproduced the experiments from an ICLR top-tier conference paper in 12 hours, with no reference code. In another, it ran continuously for 24 hours as an autonomous agent, executing nearly 2,000 tool calls without derailing. It improved FP8 matrix multiplication hardware utilization on the Hopper GPU architecture from 7.6% to 71.3% — a 9.4× optimization — entirely through autonomous scheduling and iteration. It then autonomously orchestrated the complete "data → training → iteration" pipeline on the open PostTrainBench benchmark.

No human intervention. No hand-holding. Just an agent working.

## The MSA Architecture: Rebuilding Attention for the 1M-Context Era

The key to M3's performance is MSA — Multi-head Sparse Attention — a fundamentally redesigned attention mechanism that makes 1M-token context practical rather than theoretical.

Traditional attention scales quadratically with sequence length. A 1M-token context under standard attention is effectively unusable — the compute cost explodes. Existing solutions like sliding window attention, sparse attention, or linear attention each make trade-offs that degrade performance on long-range dependencies.

M3's MSA takes a different approach: precise KV partitioning with operator-level optimization. Rather than approximating attention sparsity patterns through heuristics, MSA computes precise partitioning at the underlying operator level. The result is genuine long-range attention behavior without the quadratic cost.

At 1M context length, per-token compute cost drops to one-tenth of the previous generation. The prefill stage — where the model processes the entire context before generating — accelerates 9×. The decoding stage — where the model generates token by token — accelerates 15×. Overall inference speed at 1M context is 4× faster than comparable open-source solutions.

This matters because agent workloads are fundamentally long-context problems. An agent working for 24 hours accumulates enormous state — conversation history, tool outputs, intermediate results, error logs, planning artifacts. Models with 128K or even 256K contexts eventually lose the thread. A genuinely usable 1M context changes what's possible: multi-day autonomous runs, complex multi-file codebase operations, and long-horizon planning with full state retention.

## Training: Trillion-Scale Interleaved Data

M3 was trained on native trillion-scale interleaved data — text, code, images, and video mixed at the pretraining stage rather than bolted on later. This produces a "highly integrated semantic space" where the model reasons across modalities natively rather than through separate encoders stitched together.

The result is visible in the benchmarks: M3 doesn't just code well or see well — it reasons across code, visual data, and language simultaneously. For agent use cases, this is closer to how humans work: you look at a UI, read documentation, write code, and inspect outputs — all in one continuous cognitive thread.

## Why It Matters

The open-source AI landscape has been dominated by Western models: Llama, Mistral, Qwen, DeepSeek. M3 represents something different: a model designed from first principles for agent workloads rather than chat, and released fully open-source with no usage restrictions.

For AI application developers — the kind of company that 九地之下 represents — this changes the build-vs-buy calculus. A model that can autonomously execute multi-hour agent tasks, beats GPT-5.5 at coding, and runs efficiently at massive context lengths means you can build agent systems on your own infrastructure without paying per-token to closed-source APIs.

The 24-hour autonomous run with 2,000+ tool calls is particularly significant. Most agent demonstrations are carefully scripted — a few steps, a clean environment, a narrow task. M3's demo is messy, extended, and real. The model didn't just complete a task; it sustained coherent, goal-directed behavior across an entire day of work with no degradation.

That's not a benchmark number. That's a capability signal.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
