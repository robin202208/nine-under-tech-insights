# AI Agents Just Made Performance Engineering Cheap: There's No Reason for Software to Be Slow Anymore

> Dan Luu's latest experiment shows that performance work which used to require rare specialists — JIT compilers, multithreading, workload-specific tuning — can now be launched with a few sentences to a coding agent, collapsing the cost of optimization by orders of magnitude.

## What Happened

Dan Luu published a follow-up to his [benchmarkpocalypse post](https://danluu.com/benchpocalypse/), this time focused on the economics of performance engineering. The core observation: the cost of formerly specialized performance work has dropped by many orders of magnitude, so optimizations that were only worthwhile for the largest-scale projects are now trivially affordable for anyone.

He demonstrated this with concrete experiments. Building on FRE — the regex engine an agent loop produced in his previous post — he had an agent implement a native AOT compiler that runs in a background thread while ripgrep matches normally, cutting over when compilation finishes. On longer queries this produced 2-4x speedups, and about 7% on representative holdout queries where AOT should be enabled — a decent outcome for "a few minutes of typing to codex." He also launched a workload-specific optimization pass against his own ripgrep query history, which was already 2% faster than stock ripgrep on a holdout after one pass, still improving.

The broader evidence is striking. With no background in game AI, he built what became the strongest Azul-playing AI in the world, winning mostly on optimization (multithreading, native code, two search architectures) despite using roughly two orders of magnitude less time than the previous best. And when Jamie Brandon — a performance engineer good enough to get an offer from Anthropic — let Claude continue his own performance takehome, the model beat him, using "crazy shit that I would never try unless I was working on this for weeks."

## Why It Matters

The shift is not that AI writes faster code — it's that the *marginal cost of the optimization loop* has collapsed. Luu estimates the human-time cost of verifying tricky optimizations has dropped by 1000x to 1000000x (roughly 1000x in dollar terms), which changes the decision calculus for every 2%-here, 3%-there optimization that previously lost to "N person-days to verify."

This enables a qualitatively new kind of software: Marc Brooker's comment, which Luu endorses, is that "dynamic custom software, fitted to a particular workload rather than a class of workloads" is now a likely outcome — FFTW-style specialization without the years of expert effort. Michael Malis of pgrust makes the same point: databases and JIT compilers were historically the hardest software to build precisely because the expertise barrier was insurmountable; LLMs lower that barrier enough to make ambitious systems viable.

The hard boundary remains experimental design. SOTA models are still bad at designing benchmarks and evaluating their own optimizations without a human setting up the framework — which is exactly why Luu's agent overfit FRE to the rebar suite until a holdout was introduced. The human's job shifts from writing optimizations to constructing honest evaluation loops.

## Impact

For developers, decent performance is no longer a specialized skill: "someone who doesn't know anything about performance and is a reasonable user of LLMs should generally be able to create software that has decent performance." The appendix data on agent-generated ripgrep workloads (p99 queries near 1 minute, p999 near 10 minutes, 94% of patterns seen only once) hints that agents generate pathological workloads that will themselves become a performance problem to solve.

For the industry, expect workload-specific optimization services — vendors tuning their software against a customer's actual query/data distribution — to become a real business, as both Amazon-scale and pgrust-style players explore. The risks are equally real: overfitting to a workload that regime-changes, and the temptation to optimize what is measured rather than what matters. The discipline that saved the FRE project — a holdout you respect — is the same discipline that will separate useful optimization from benchmark theater.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [danluu.com](https://danluu.com/perf-opt/) | HN Discussion: [620 points, 480 comments](https://news.ycombinator.com/item?id=49395628)*
