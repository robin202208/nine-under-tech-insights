# Four Mac Studios, One 816B Model: Apple Made the Interconnect the Product

> A cluster of four desktops now serves a model that fits on none of them — and the interesting part is the cable.

## What Happened

On October 15, Apple's machine learning research group published benchmarks for running frontier-scale models across multiple Mac Studios, released alongside MLX v0.30.1. The payload was Meta's Muse Spark Open: 816B total parameters, 42B active, a mixture-of-experts architecture. In native BF16 it needs more than 1.6 TB just for weights; quantized to NVFP4 it still occupies roughly 493 GB before activations and KV cache — which means it cannot fit on a single Mac Studio, including the 512 GB M5 Ultra.

Apple's answer is to split it. Across four M5 Ultra Mac Studios, tensor parallelism — the model cut across its width, every layer sharded — delivers 36.7 tokens per second for Muse Spark Open, against 25.0 on two machines and 15.7 for two-machine pipeline parallelism. Dense models make the scaling even more legible: Qwen 3.8 27B rises from 19.7 tok/s on one M5 Ultra to 50.9 on four, and Mistral Medium 3.5 128B from 7.7 to 23.4. Time to first token for the 128B dense model drops from 95.9 s on an M3 Ultra to 24.6 s on one M5 Ultra and 7.5 s across four.

The enabler is not the model or the chip. macOS 26.2 added a low-latency RDMA driver over Thunderbolt 5, and MLX builds a collective-communication layer called `jaccl` on top of it, launched through `mlx.launch --hostfile`. Machines are wired peer-to-peer with two Thunderbolt cables per link to double bandwidth. With that fabric, pipeline parallelism — split by depth, nearly free in code changes — keeps decode flat but makes prefill up to ~1.8× faster at 65,536-token context, because MLX chunks the prompt at 8,192 tokens and overlaps chunk k+1's early layers with chunk k's later ones. Tensor parallelism is harder, since each layer must broadcast inputs and reduce outputs, but it pays: adding a second cable per link on a four-machine mesh cut Muse Spark Open's 65k-context TTFT from 50.5 s to 40.6 s.

## Why It Matters

Local inference has been governed by a single question: does the model fit in unified memory? Past about 500 GB the answer is no, and the workarounds were poor — quantize until quality degrades, or shard across machines over Ethernet, where a per-layer all-gather for every token drowns in TCP latency. Decode is memory-bandwidth bound, so a fabric that adds microseconds rather than milliseconds is what makes tensor parallelism viable at all. RDMA over Thunderbolt gives a small cluster something close to a PCIe backplane without a switch, an InfiniBand card, or a rack.

The benchmarks also show that the right parallelization is a function of which phase you are in. Decode wants aggregate memory bandwidth and scales close to linearly with machines; prefill wants compute and only gains from pipelining once the context is long enough to fill the pipeline. Apple's own data carries the exception: MoE prefill spends extra traffic normalizing expert outputs, so at 65k context two-machine tensor parallelism actually trails two-machine pipeline parallelism — before four machines reverse the ordering. That is a scheduling detail most "run it locally" guides never reach — and it decides whether a second machine speeds you up or slows you down.

## Impact

The practical consequence is that a private, frontier-class agentic coding stack no longer requires a datacenter. Apple demonstrates `mlx_lm.server` wrapped in `mlx.launch`, then points OpenCode at `http://127.0.0.1:8080/v1` — an OpenAI-compatible endpoint serving an 816B model at 65k context, roughly 37 tok/s single-stream, drawing about 300 W per machine. No egress, no provider keys, no rate limits, no per-token bill.

For teams building on Apple silicon, three things change. Interconnect becomes a first-class design axis: budget for Thunderbolt links and a hostfile, not just RAM. Model choice becomes a topology choice: dense architectures scale better across machines, MoE architectures need more bandwidth to achieve the same. And benchmarking has to be phase-split, because TTFT and decode tell different stories about the same configuration.

The caveats are real. Every published number is batch-1, single-stream; Apple reported no batched throughput, so the per-user economics of a shared cluster remain unknown. A 65k-token prompt still costs 40 s of prefill on four machines, and roughly 2 TB of aggregate memory is holding 493 GB of weights — utilization near a quarter. The `MLX_METAL_FAST_SYNCH` flag is described as experimental. But the direction is unmistakable: for the first time, the interesting question about a Mac cluster is not how much memory it has, but how fast the cable between the machines is.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Apple Machine Learning Research](https://machinelearning.apple.com/research/mlx-distributed-inference) | HN Discussion: [362 points, 195 comments](https://news.ycombinator.com/item?id=50118320)*
