# TypeSafe's Jev: The Model That Refuses to Write Sentences

> Give up strings, the pitch goes. TypeSafe's first model returns typed, calibrated decisions in a single parallel pass — pitched at 444x cheaper than the LLMs it targets.

## What Happened

TypeSafe AI emerged from two years of stealth this week with "System One models," a claimed new class of frontier model built to make fast, structured decisions that software can consume directly. Founder Diogo Almeida says he worked at OpenAI on the instruction-following and preference-tuning research behind ChatGPT; he now argues chat models are the wrong shape for automation. The first public model is Jev, in early access.

The technical bet is blunt: Jev does not generate strings. LLMs emit tokens one at a time, each conditioned on the last. Jev returns every output in a single query, constrained to type-safe structured values whose possible structure is fixed by a schema — so it "never makes type errors" — and each answer carries a calibrated probability. Under the hood: a new architecture, a parallel sampler, and a training method called Reinforcement Learning for Calibrated Decisions (RLCD), positioned against RLHF (human preference) and RLVR (verifiable rewards).

The economics are the headline. Input costs $0.042 per million tokens, output tokens are free, "too cheap to meter," against $0.20–$10 per million input plus roughly 5x that for output on standard LLM APIs. Latency is 70–500 ms versus seconds: 40x to 200x faster on System One-shaped queries, with a home-page claim of 193.6x faster and 444.6x cheaper on four published workflows that TypeSafe concedes sit at the high end of real-world gains.

Those workflows define their own eval: instead of letting each model tune its harness, every model runs the same fixed compute-graph workflow written in code, scored against reference probabilities averaged from GPT-6 Astra and Fable 5.1 — deliberately biased toward incumbents, they note. Two demos make it concrete: a reactive Doom bot at about 10 queries per second for roughly $7/hour, and a Wikipedia traversal game selecting among up to 255 links per step. The names nod to Kahneman's System 1 and to Jevons: cheaper intelligence unlocks more demand.

Skepticism is warranted: the launch drew 719 points and 241 comments on Hacker News, where readers asked for the demo code rather than animations. There are no third-party benchmarks, and the architecture is described, not published.

## Why It Matters

The automation gap is real: chat models have been superhuman at conversation for years, yet most business processes remain unautomated. TypeSafe's diagnosis is that the interface is the bottleneck. When the only output is a string, everything downstream is parse, validate, retry, escalate — hallucinated tool calls, malformed JSON, silent divergence, seconds of latency. That friction compounds through dependency chains, where one bad call breaks a system meant to be reliable.

Typed decisions change the failure mode. If the output space is constrained and schema matching is guaranteed, a model call becomes a fuzzy if-statement that slots into ordinary code: classify, route, score, extract, branch where hand-written logic is too brittle. Confidence scores let callers set thresholds and reason about aggregate error — precisely what TypeSafe says is missing, since a model right 95% of the time but silent about the 5% cannot run unattended. And 70–500 ms at $0.042 per million tokens changes what is feasible: decisions once too slow or costly per call to sit inside an interactive application become affordable ten times a second.

Separate the product claim from the methodological one. TypeSafe's eval fixes the workflow and measures probability quality, rather than letting teams tune prompts and tests until scores rise — a direct jab at benchmark culture. Whether their eval becomes a standard is open; that the argument arrives with receipts is notable.

## Impact

For developers, a credible typed-decision API relocates engineering: guardrails, judges, routers, and extractors stop being prompt-shaped services and become cheap functions with measurable error rates. Pipelines could spend model calls where they previously spent retries.

For infrastructure, the latency budget moves from seconds to milliseconds — the difference between AI bolted onto a workflow and AI inside the interaction loop.

For labs, the pressure shifts toward calibration: publishing probabilities that can be checked, not just answers that can be admired. That is harder to market than a benchmark win, and easier to falsify.

The honest reading: this does not prove strings are dead. It makes "no strings" a defensible product category — with the caveats that everything is vendor-reported, early access, schema-bound, and unverified by outsiders. If the numbers survive independent testing, the interesting question stops being whether models can talk, and starts being whether we can trust the numbers they hand back.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [TypeSafe AI](https://typesafe.ai/blog/introducing-system-one-models-and-jev) | HN Discussion: [719 points, 241 comments](https://news.ycombinator.com/item?id=49717558)*
