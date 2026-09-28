# Fireworks Cut Kimi K3's Reasoning Tokens by 40% — and the Argument Underneath Is Bigger Than the Model

> Ember-1 matches its base model's quality while burning roughly 40% fewer tokens. The interesting part isn't the model — it's the claim that in multi-turn agents, reasoning tokens are quadratic, and you cannot configure your way out of them.

## What Happened

On September 23, Fireworks Research — the research arm of inference provider Fireworks AI — released **Ember-1**, a specialized model built on top of Kimi K3 that "delivers Kimi K3's quality with 40% fewer tokens." It ships as a serving option alongside the base model on Fireworks' serverless platform, at the same list price as K3 ($3 per million uncached input tokens, $0.30 cached, $15 output), as a two-week research preview.

The pitch is narrow and empirical. Fireworks says users wanted K3's coding ability without its long reasoning traces, and that turning the model's reasoning effort down didn't help — the lower-effort arms gave up too much accuracy. So Fireworks trained the efficiency in instead: over 50 training experiments and 200 evaluations, across math, coding, tool use and software engineering, with task feedback driving on-policy planning. No customer data was used.

The results, all self-reported:

| Benchmark | K3 (low → max) | Ember-1 | Cost/task vs K3-max |
|---|---|---|---|
| Terminal Bench 2.1 | 76.4% → 80.9% | **82.0%** | −51.9% |
| SWE-bench Verified (n=500) | 80.4% → 93.2% | 92.2% | −15.5% |
| SWE-Interact | 6.7% → 21.3% | 20.0% | −32.5% |
| DeepSWE 1.1 (n=113) | 55.8% → 66.4% | **75.2%** | −23.7% |
| τ-2 Bench Airline | 64% → 64% | **66%** | −5.9% |

In live A/B tests on two customers' production coding traffic, quality held at 0.751 → 0.753 while output tokens per task fell from 49.3K to 29.9K — a 71.3% reduction in reasoning tokens and 39% overall — and steps dropped from 23.8 to 21.4. Fireworks also ran Ember-1 on its own developers' coding traffic before any customer saw it. Nobody noticed.

## Why It Matters

The number to take seriously isn't 40%. It's the mechanism Fireworks gives for why reasoning is so expensive: thinking models spend the majority of their generated tokens — sometimes more than 90% — on internal reasoning rather than the answer. In a multi-turn agent loop every turn replays all prior reasoning back into context, so context grows roughly quadratically with the number of turns. Long traces from early turns get re-read and re-billed on every subsequent call.

That reframes the fix. If lower effort settings really do cost accuracy — K3 at low scores 80.4% on SWE-bench Verified and 6.7% on SWE-Interact — then the waste is not a knob you can turn. It is a behavior you have to train out, while preserving the self-reflection that actually recovers from mistakes. Fireworks' claim is that this is learnable, so the right unit of account becomes not dollars per million tokens but dollars per finished task.

It also marks a category shift: Fireworks was, until now, a neutral host. "Specialized intelligence" — take an open base model and post-train it for your workload's efficiency profile — is the business model spelled out in the post, and Fireworks is selling the training platform that produces it. The same trick is already a community sport: practitioners have spent months distilling shorter reasoning traces into Qwen-class models on rented GPUs.

## Impact

For teams running agents, the practical shift is measurement. Track tokens-per-completed-task and cost-per-completion on your own traffic. A model with identical per-token pricing that burns 40% fewer tokens is a 40% cut on the dominant line item in long loops — but only if your workload is decode-bound. Practitioners in the launch thread pushed back: much of today's agentic coding bill comes from prefill and caching, not decode, so the savings may be smaller than advertised.

Four caveats are worth pricing in. Every number is vendor-reported, with no independent evaluation. The weights are not released, even though the base model's are. Capability regressions are not broken out — what Ember-1 got worse at goes unstated. And the "Pareto frontier" claim rests on Fireworks' own Specialized Intelligence Index and a single 500-case clinical benchmark. There is a structural conflict too: Fireworks now both hosts open models and competes with them, which some commenters read as grounds to re-check that your data is never used for training.

The technique itself is not proprietary. Post-training a model to think more briefly is reproducible, and the cheapest way to price it is to measure your own token bill per finished task — not the vendor's chart.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Fireworks AI — Introducing Ember-1](https://fireworks.ai/blog/ember-1) | HN Discussion: [352 points, 180 comments](https://news.ycombinator.com/item?id=49868830)*
