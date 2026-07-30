# A 26B Model Just Ran in 2GB RAM on an 8GB Mac — And It's Open Source

> TurboFieldfare, a custom Swift + Metal inference engine by Andrey Mikhaylov, runs Google's Gemma 4 26B-A4B on any M-series Mac using only ~2 GB of RAM. On an 8 GB M2 MacBook Air, it generates at 5–6 tok/s. On an M5 Pro, 31–35 tok/s. The weights never fully fit in memory. That's the point.

## What Happened

Andrey Mikhaylov, an iOS and Metal engineer at Lapse (ex-Prisma AI), released TurboFieldfare — an Apache 2.0-licensed inference engine purpose-built for a single model: Gemma 4 26B-A4B. The project hit Hacker News today and rocketed to 1,000+ stars in under 12 hours.

The headline number is arresting: a 26-billion-parameter model running in ~2 GB of RAM on an 8 GB M2 MacBook Air. The 4-bit quantized weights occupy roughly 14.3 GB on disk — yet only about 2 GB are resident in memory at any moment. The trick is architecturally specific.

Gemma 4 26B-A4B is a Mixture-of-Experts model. It has 26B total parameters but activates only ~3.88B per token. TurboFieldfare exploits this by keeping the shared core (1.35 GB) and FP16 KV cache in RAM permanently, then streaming only the routed experts needed for each token from SSD. A 16-slot LFU cache per layer keeps hot experts close. When a cache miss occurs, bounded parallel `pread` calls fetch the missing expert blocks while the GPU continues computing the shared-expert branch of that layer — effectively hiding SSD latency behind ongoing Metal compute.

The engine is written entirely in Swift 6.2 and Metal 4, targeting macOS 26. It's not a wrapper around MLX or llama.cpp. It's model-specific. It ships as a native Mac app, a CLI, a streaming installer that never materializes the full checkpoint, and an experimental OpenAI-compatible local server with streaming and tool-call support.

The repo documents 103 experiments. Most failed. The ones that worked produced a cold 0.50 tok/s with naive `mmap` becoming 4+ tok/s with tuned `pread` and expert caching.

## Why It Matters

This isn't a benchmark stunt. It's a working artifact that redefines what "runs locally" means. The gap between "model size" and "available memory" has been the fundamental constraint of on-device AI. TurboFieldfare shows that constraint is negotiable — not through compression magic, but through architectural alignment between the inference runtime and the model's structure.

The implications cascade. An 8 GB M2 MacBook Air, the cheapest Mac Apple sells, can now run a frontier-class 26B instruction model at conversational speeds. That machine cost $999 in 2022. It requires no internet, no API key, no per-token billing. Your data stays on your device. For developers in privacy-sensitive domains, students in low-connectivity regions, or anyone who's watched their API bill climb, this changes the calculus.

The M5 Pro result — 31–35 tok/s — suggests that as Apple Silicon SSDs get faster (the M5 SSD reads at 6,323 MB/s vs. the M4's 2,031 MB/s), SSD-streamed inference converges on usable real-time speeds. The bottleneck shifts from "can I run this model" to "how fast is my SSD." That's a hardware problem with a clear trajectory.

There's also a quiet architectural lesson. TurboFieldfare is model-specific. It doesn't try to be a general runtime. It exploits every structural property of Gemma 4's MoE design — the expert count, the expert sizes, the shared/routed split. The result is dramatically better than any general-purpose engine on the same hardware. As model architectures diversify, we may need fewer general runtimes and more purpose-built ones like this.

## Impact

The most immediate signal is the open-source community's response: 1,000+ stars in half a day, dozens of forks, active benchmark contributions from M1 Max, M4, and M5 owners. The project ships a community benchmark guide so anyone can contribute a data point. That flywheel — more hardware profiles → better cache tuning → higher speeds — is already spinning.

For Google, this is free distribution of Gemma 4 into environments it could never reach through cloud APIs. TurboFieldfare includes an OpenAI-compatible server, meaning any tool that speaks the Chat Completions API (OpenCode, Continue, Aider) can now point at a local Gemma 4 running on a base-model MacBook Air. That's a distribution channel no cloud contract can buy.

For the broader inference ecosystem, the open question is whether similar SSD-streaming techniques can be generalized. Projects like Flash-MoE and Colibri are exploring the same space, but TurboFieldfare's approach — model-specificity plus exhaustive empirical optimization — sets a high bar. Mikhaylov has already invited collaboration with Matt Mastrac's DiffusionGemma engine for faster kernels.

The deeper story is about access. When a 26B frontier model runs on a three-year-old entry-level laptop with no cloud dependency, the definition of "AI-capable hardware" expands. The 8 GB MacBook Air was never supposed to run models this large. TurboFieldfare says it can. The next question is what else we've been wrong about.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [TurboFieldfare on GitHub](https://github.com/drumih/turbo-fieldfare) | HN Discussion: [640 points, 223 comments](https://news.ycombinator.com/item?id=49098510)*
