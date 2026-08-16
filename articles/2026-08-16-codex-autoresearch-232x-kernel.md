# The 232× Kernel That No Human Wrote: What a 14-Day Codex Autonomy Run Teaches About AI Research Loops

> One developer handed an autonomous Codex loop a GPU kernel contest and came back with a 232× speedup over cuSolver-level baselines — by learning how to steer agents out of local maxima, not by writing kernels himself.

## What Happened

GPU Mode and Core Automation ran an auto-research contest: implement batched, square, compact Householder QR factorization — the same output as `torch.geqrf` — on B200 GPUs. The baseline (cuSolver-equivalent PyTorch paths) ran around 419,000 µs. Developer Sankalp placed 12th of 183 with a final tracked runtime of 1,805 µs: a **232× speedup**, achieved over 14 days and more than 1,500 automated submissions.

The winning arc was an exercise in harness design. He set up a workspace where Codex (OpenAI's CLI agent) had everything it needed to self-iterate: a `problem_statement.md`, an `AGENTS.md` encoding submission discipline and beam-search rules, a log of every submission's accept/reject status and shape-wise timings, the `popcorn` CLI for testing and leaderboard submission, and Modal/NCU profiling access. Then he launched `/goal` loops that ran for a day or more, checking in every 2-3 hours via side threads (`/btw`, `/side`) without pausing the loop.

The kernel itself evolved through recognizable structural phases: blocked Householder with WY representation (confining serial panel work to a narrow panel, then compressing reflectors into a rank-b update shaped as three GEMMs), Triton panels, CUDA graph replay to kill launch overhead, fused layout assembly, fixed-shape kernel specialization, and a Cholesky-based ORHR route for the largest 4096×4096 cases. The result: library-grade numerics rebuilt as agent-optimized custom kernels.

## Why It Matters

The striking part is where the difficulty actually sat. After the first ~10× (achievable in a day with standard blocked algorithms), progress became an exercise in *research taste*: given profiler feedback and prior evidence, what is the next best experiment? The author's main bottleneck was the agent getting stuck in local maxima — endlessly hand-tuning parameters and small variants of the same idea.

His fixes are the real contribution. First, a **beam of 3–5 candidate idea families** instead of single-incumbent hill climbing, because structural changes almost always score worse before they score better. Second, an **advisor model**: headless `claude -p` calls feeding fresh ideas to the main loop — the pattern of pairing a strong advisor with a cheaper executor that Anthropic is productizing. Third, explicit instructions to take risks, use sub-agents for ambitious branches, and treat timeouts as inconclusive rather than rejections. Fourth, meticulous logging so future sessions never re-try a dead idea.

This reframes the auto-research debate. A year ago, agent loops were limited by model capability and fragile verification. Now the models are smart enough that the constraint is orchestration: how to keep idea diversity alive, how to document evidence, and when a human should inject a steering signal. The author notes that a principal engineer at NVIDIA sat just above him on the leaderboard — domain knowledge still matters, but it now manifests as better prompts, better harnesses, and better steering, not better code.

## Impact

For developers, this is a working template for agent-driven engineering: `AGENTS.md` as a persistent constitution, logs as the memory that makes autonomy safe, profiling loops as the reward signal, and a beam search over ideas instead of hill climbing. Coding agents can now run multi-day optimization campaigns that no human has the patience for — and beat vendor libraries while doing it.

For the industry, the implications reach past kernels. QR-style decompositions underpin modern LLM training optimizers like Muon and Shampoo-style preconditioning — the same algorithms being agent-optimized here are the ones sitting inside training loops. And the "advisor + executor" multi-model pattern is becoming the standard architecture for agentic research. The 232× number is impressive; the transferable lesson is that in 2026, the ceiling on autonomous AI engineering is no longer the model — it's the design of the loop around it.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Auto-research with codex: How I achieved a 232x Faster Kernel](https://sankalp.bearblog.dev/autoresearch/) | HN Discussion: [391 points, 86 comments](https://news.ycombinator.com/item?id=49309549)*
