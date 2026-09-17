# A 4B Model Beat Postgres's Own Query Planner for $1,200

> Rohan Bansal distilled GPT-6 Astra into a 21M-parameter adapter, then trained it with reinforcement learning against a deliberately de-noised Postgres benchmark. Result: 44.7% less latency across 113 join-heavy queries.

## What Happened

Query optimization is a hard problem hiding in plain sight. Join ordering is NP-hard, and Leis et al. re-asked their own 2015 question — *How good are query optimizers, really?* — a decade later and found the answer still disappointing. Rohan Bansal, on sabbatical at the Recurse Center, attacked it as a reinforcement learning problem: Postgres execution time is a verifiable scalar reward, so the plan-generation task reduces to reinforcing whatever produces faster plans. He published the full experiment on September 16.

The setup: a 4.66B-parameter open-weights Qwen model that emits `pg_hint_plan`-style directives (`/*+ Leading((t cn) mc) */`, `NestLoop`, `Parallel`, `enable_sort=off`) inside an agent harness. Untrained, it was useless — 99 of 113 queries ended with no valid plan at all, and it could not even call the harness's tools correctly. So Bansal ran off-policy distillation first: 420 trajectories from GPT-6 Astra (100 training + 20 validation, then 300 more), rendered into Qwen token format, loss-masked so only model-authored tokens were scored, unrolled into (context, reply) pairs and packed — 100 trajectories became 382 training rows. The trainable LoRA was 21.2M parameters, 42.5 MB on top of a 9.32 GB bf16 base, and the first epoch took four hours on a single RTX 3090.

Then came agentic RL. Each rollout proposes a candidate, Postgres measures it against its own default plan, and rewards — `ln(speedup)` minus fees for invalid or duplicate plans — flow back. Final checkpoint after 1,200 optimizer updates: 1.41x geometric-mean speedup, 101 of 113 queries producing valid plans. Allowing three rollouts per query and picking the best of up to 15 candidates lifted that to **1.81x geometric mean and 1.81x total workload speedup, with 68 wins and zero regressions**.

## Why It Matters

The most valuable engineering here is not the model, it is the measurement rig. Bansal ran four concurrent Postgres containers on one machine and discovered the reward signal was partly fiction. With `shared_buffers` at 128 MB against an 8.5 GB IMDb database, every query kept missing Postgres's cache and rereading from Linux — and one query, `job-13b`, was bimodal: 14 runs at 186–204 ms, 6 runs at 227–253 ms. A no-op candidate identical to the default would be scored as a 14–26% win or loss roughly 20% of the time. He built the honest metric: a simulated "fooling rate" measuring how often the reward misleads, given three interleaved candidate/default pairs and a 5% tie zone. At 128 MB, the mean no-op error rate was ~5% and the p90 query 13–20%.

Raising `shared_buffers` to 2 GB collapsed that to ~1.7% and 0%, took `job-13b`'s coefficient of variation from 10.3% to 0.9% — and made the *default* plans faster too, cutting the 113-query workload from 95 s to 60 s. Noise and speed were the same knob.

The second lesson is that stock GRPO quietly rewarded failure. With a harsh −3 penalty for invalid plans, the model played safe and kept returning Postgres's own plan; worse, in a group where every rollout was bad, one still received positive advantage for being the least bad. Bansal swapped in an anchored variant whose baseline is the maximum of zero and the siblings' mean quality, so mediocre groups are down-weighted instead of partially reinforced.

## Impact

Frontier models are not going anywhere here — Astra with five candidates scored 2.54x geometric-mean speedup on a 10-query slice, and with only one candidate it scored 0.85x, *worse than Postgres*, evidence that the gains come from in-context learning across attempts rather than one-shot genius. The distillation is what transferred that competence into a small student.

The economics are the point: ~$800 of H100 rental plus ~$400 of teacher API calls, $1,200 total, for a specialist model that runs cheaply and fast. Bansal's conclusion is aimed at companies that already own domain data and a measurable objective. Build the environment, verify the reward is not fooling you, and post-train a small open-weights model. The 4B model also learned recognizably human tuning heuristics — 295 of 337 searches began by inspecting statistics or the default plan, and the favorite levers were scan hints, `Leading` trees and `Parallel`, not row-count corrections.

One caveat worth internalizing: three SFT epochs performed *worse* than two while validation loss stayed flat at ~0.31. Useful behavior was still being learned inside a number that looked converged.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [rohanbansal.com](https://rohanbansal.com/qorl) | HN Discussion: [382 points, 81 comments](https://news.ycombinator.com/item?id=49731285)*
