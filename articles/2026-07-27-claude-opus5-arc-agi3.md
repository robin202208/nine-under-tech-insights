# Claude Opus 5 Nearly Quadruples ARC-AGI-3 Record — And Shows Signs of Genuine Reasoning

> Anthropic's latest model scored 30.2% on the hardest reasoning benchmark, blazing past GPT-5.6 Sol's 7.8%. But the real story isn't the score — it's what the model did during testing.

## What Happened

On July 26, 2026, the ARC Prize team announced that Anthropic's Claude Opus 5 achieved 30.2% on ARC-AGI-3, the benchmark designed to measure how well AI models solve genuinely novel tasks. The previous record — 7.8%, held by OpenAI's GPT-5.6 Sol (Max) — now looks like a rounding error. Opus 5's score represents a near-4× leap in a single generation.

ARC-AGI-3 is not a memorization test. It presents models with interactive environments — essentially puzzle games — where the model must infer hidden rules, plan a sequence of actions, and execute them step by step. There are 25 public demo environments, each demanding on-the-fly reasoning rather than retrieval of training data. Opus 5 solved five environments that no model had cracked before, four of them at or above human-level performance. Six of the 25 environments have now been solved total.

More revealing than the score, however, was the behavior. Researchers observed Opus 5 doing something no prior model had done: it spontaneously translated tasks into algebraic notation and independently formulated reflection equations — essentially, it developed its own symbolic reasoning system during evaluation. This was not prompted or scaffolded; it emerged from the model's internal reasoning process.

At the same time, Opus 5 reached 90.4% on ARC-AGI-2 and 97.5% on ARC-AGI-1, matching previous top scores on both older benchmarks. The full results, evaluation replays, and benchmarking code are publicly available on the ARC Prize site.

## Why It Matters

The ARC-AGI benchmark series was designed by François Chollet and Mike Knoop as a measure of fluid intelligence — the ability to adapt to novelty — rather than crystallized intelligence (stored knowledge). Most LLM benchmark gains come from training on test-like data, which ARC-AGI-3's interactive, environment-based format makes substantially harder to saturate.

This is why the 30.2% matters. It suggests Anthropic has made real progress on the hardest part of AI: reasoning that transfers to unfamiliar situations. The ARC Prize team explicitly attributed the gain to "stronger logical reasoning, which enables more autonomous exploration, planning, and execution across unfamiliar environments."

However, the results are not without controversy. Opus 5 was developed after ARC-AGI-3's format became public, and independent tests on Witness — a private benchmark for interactive puzzle games by researcher Guanghan Ning — show much narrower gains. Opus 5 scored 43.4 on Witness, statistically tied with Kimi K3 and Fable 5, and actually performed worse than Opus 4.8 on one puzzle with unfamiliar rule combinations. Ning suggests this pattern is consistent with training on genre-specific data rather than genuine generalization.

Greg Kamradt, one of the ARC-AGI-3 researchers, pushed back: models consistently show isolated regressions even amid broad improvements, and one weak result does not negate the overall trend. Ning himself later clarified that Opus 5 did generalize to Witness overall — just far less dramatically than on ARC-AGI-3. He compared this to the historical evolution of coding benchmarks: early saturation of HumanEval gave way to harder, frequently updated contests, and eventually to today's coding agents.

The tension between "genuine reasoning breakthrough" and "benchmark optimization" is not going away. What is clear is that Anthropic has raised the ceiling by an amount nobody predicted.

## Impact

For AI developers, Opus 5's performance changes what "state of the art" means for reasoning. Models are now expected not just to answer questions correctly, but to explore, plan, and self-correct in unfamiliar environments. The spontaneous algebraic reasoning — entirely unprompted — suggests that future models may develop increasingly sophisticated internal reasoning strategies that their creators did not explicitly design.

For the broader industry, the ARC-AGI-3 result accelerates the conversation about what AGI actually looks like. Chollet has long argued that the path to AGI runs through program synthesis and discrete reasoning, not scaling alone. A model that invents its own symbolic notation during problem-solving lends weight to that thesis.

The near-term practical takeaway: reasoning is now the primary battleground. Every major lab — Anthropic, OpenAI, Google DeepMind, Moonshot — is racing to build models that don't just know more, but think better. Opus 5 just raised the stakes.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [The Decoder](https://the-decoder.com/anthropics-opus-5-blows-past-fable-5-and-gpt-5-6-sol-on-the-benchmark-designed-to-measure-real-intelligence/), [ARC Prize](https://arcprize.org/leaderboard) | HN Discussion: [170 points, 144 comments](https://news.ycombinator.com/item?id=49045040)*
