# An AI Just Beat a Two-Year Human Optimization Effort — on Three Benchmarks at Once

> Recursive's automated AI research system achieved state-of-the-art on language model training, training speed, and GPU kernel optimization — including beating 83 human contributions to the NanoGPT Speedrun leaderboard. The system proposes, implements, validates, and learns from experiments in a closed loop. All artifacts are open-source.

## What Happened

On June 11, Recursive released early results from its automated AI research system — an AI that runs the full research loop autonomously: it proposes an idea, implements it in code, runs the experiment, validates the result against reward hacks, and uses what it learns to choose the next experiment. It runs many research threads over long horizons, keeps useful context from prior experiments, combines promising branches, and validates results before treating improved performance as real progress.

The system was tested on three benchmarks chosen for both practical importance and tight feedback loops. On all three, it achieved state-of-the-art.

On **NanoChat Autoresearch** — a fixed-budget language model training task popularized by Andrej Karpathy — the system found a solution reaching **0.9109 BPB**, beating the best solution from the autoresearch@home community (0.9372 BPB), a collaborative effort involving dozens of humans and hundreds of their AI agents. This represents a 1.3× speedup to reach the same loss.

On **NanoGPT Speedrun** — training a small GPT model to a fixed validation loss as fast as possible — the system reduced training time from **79.7 seconds to 77.5 seconds**. This matters because the Speedrun leaderboard has been optimized by 83 human record-setting contributions over two years. The system's 2.2-second improvement is comparable to or larger than recent human contributions.

On **SOL-ExecBench** — 235 GPU kernel optimization tasks derived from real workloads — the system achieved a mean Speed-of-Light score of **0.754**, an 18% reduction in the gap to the hardware limit from the previous best of 0.699.

## How the System Works

The system automates the entire research loop. It proposes an idea, implements it, runs an experiment, validates the result, and uses what it learns to choose the next experiment. It runs many research threads over long horizons, keeps useful context from prior experiments, combines promising branches, and puts results through validation for reward hacks and variance before treating improved performance as real progress.

Crucially, the system compounds discoveries. The winning solutions were never driven by one trick. For NanoGPT Speedrun, the 77.5-second solution combined FP8 attention projections (custom cuBLAS integration with bf16 backward pass), annealed exploration noise in the optimizer (Langevin/SGLD injection into NorMuon), cautious sign-agreement Adam on embedding tables, a leaner fused MLP kernel that eliminates activation tensor round-trips, and several schedule-level choices.

For NanoChat, one of the biggest gains came from hashed bigram and trigram embedding tables — a way to add 1-2 billion sparse parameters to a ~50M parameter model with near-zero compute cost, mixed into the attention value path through learned per-head gates with different hash functions per layer to reduce collisions.

The system also demonstrated versatility: starting from a naive vanilla Transformer with AdamW (1.059 BPB), it independently assembled a competitive training stack reaching 0.9344 BPB — outperforming the community's best while arriving at a different architectural composition.

## Why It Matters

Automated AI research has been a holy grail for AI safety and capability researchers alike. The central question has always been: can AI systems meaningfully contribute to AI progress itself, or are they limited to narrow hyperparameter tuning? Recursive's results are the strongest affirmative answer yet.

The significance isn't just the benchmark numbers — it's the breadth. The same system architecture, without per-benchmark tuning, achieved SOTA across model architecture design, training optimization, and low-level GPU kernel engineering. This suggests the research loop generalizes across abstraction levels, from high-level modeling choices to register-level CUDA optimizations.

The open-source release amplifies the impact. Recursive published artifacts from all runs on GitHub, allowing independent verification and building. In a field where progress claims often come without reproducibility, this sets a standard.

The reward hacking mitigation is also significant. The system includes an iterative anti-hacking detector that improves alongside the search — an adversarial dynamic that mirrors the core challenge of aligning recursively self-improving systems. As the company notes, "aligning such systems to solve the spirit of the task, and not its letter, will be a grand challenge."

## Developer Impact

The immediate practical signal is that AI systems can now optimize the infrastructure that builds AI itself. GPU kernel optimization — the third benchmark — directly affects the cost and speed of every training and inference workload. An 18% reduction in the gap to hardware limits across 235 real-world kernels translates to real dollar savings at scale.

The hashed n-gram embedding technique discovered by the system is directly transferable: it's a cheap way to inject sparse parametric capacity into small models without meaningful compute overhead. Teams training small, fast models for edge or mobile deployment should take note.

Longer term, the results validate the thesis that automated research can reduce the cost of intelligence — not by building bigger models, but by finding better engineering tradeoffs in existing systems. Recursive frames this explicitly: "AI progress does not come only from larger models and more compute; it also comes from making existing systems train faster, run cheaper, and use hardware more effectively."

The open question is whether the system can progress from well-defined benchmarks with clear metrics and low variance to open-ended research problems. The gap between optimizing NanoGPT training and discovering a new attention mechanism is substantial. But the trajectory is unambiguous — and it's accelerating.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
