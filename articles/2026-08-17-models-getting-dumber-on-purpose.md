# The Deliberate Trade: Why Frontier Labs Are Making Models Dumber on Purpose

> Reasoning scores keep climbing while per-token compute keeps dropping — and the price is paid in world knowledge, on purpose.

## What Happened

There is a paradox at the heart of the current AI curve. On reasoning benchmarks, models look absurdly smarter per parameter. GLM-5.2 scores 99.2% on AIME 2026 with roughly 40 billion active parameters per token; Qwen3.5 hits 91.3% with 17 billion; DeepSeek V4-Flash reasons with just 13 billion. GPT-4 reportedly ran around 280B active parameters in 2023 — and could barely solve an AIME problem. At the small end, Qwen3.5 9B fits in 6GB of quantized VRAM and roughly doubles the score of the next-best sub-10B model.

Ask those same models a plain factual question and the picture flips. On SimpleQA, a benchmark of factual recall with no tools allowed, the leader is Gemini 2.5 Pro at 53% — the best recall money can buy still misses half the questions. Artificial Analysis measures Qwen3.5 4B and 9B at hallucination rates of 80–82% on its knowledge benchmark: when they don't know a fact, they make one up. Ask the 9B for the birth year of a minor 19th-century mathematician and you get a confident, plausible, wrong answer. This is not a side effect. Labs are deliberately trading world knowledge for reasoning skill, and the trade is visible in the architecture.

## Why It Matters

The economics of weights explain the trade. Research on knowledge capacity (the "Physics of Language Models" series has the cleanest measurements) puts factual knowledge at roughly two bits per parameter. Trillion-parameter frontier models were partly built to store facts. Reasoning, by contrast, compresses far better: it is a small set of procedures applied repeatedly — break problems down, track state, check your own work, backtrack on failure. Distillation and reinforcement learning on verifiable tasks transfer those procedures into small models remarkably well. Phi-4, 14 billion parameters trained heavily on synthetic textbook data, is good at math and bad at trivia — a mix that used to look like a limitation of synthetic data and now looks like the design goal.

The deeper argument is shelf life. A frontier training run costs hundreds of millions of dollars, and the moment it finishes, the facts inside it begin rotting: APIs change, prices change, people change jobs. Half of what a 2024 model believed about the JavaScript ecosystem was outdated before it shipped. Procedures don't rot. Algebra worked the same way in 1970, and so does breaking a problem down or spotting a contradiction between sources. A model that is mostly procedure and lightly loaded with facts doesn't age the way a knowledge-heavy model does. Its training cutoff matters less, because the current state of the world was never supposed to live in the weights. This decouples the expensive, slow artifact — the trained model — from the thing that changes daily: what is true.

## Impact

If the model doesn't know things, something else has to, and that something is the harness: retrieval over a knowledge base, tool calls, web search, a filesystem full of docs. You can already watch agents work this way. A coding agent doesn't memorize your dependency's API surface — it greps `node_modules` or reads the docs before calling anything, grounding its answer in the version you actually have installed rather than whichever dominated the training data. Recall has shifted from a fixed cost in every forward pass to an on-demand lookup.

Two consequences follow. First, the frontier moves onto consumer hardware: strip the expert layers that mostly store facts, and total model size shrinks toward active size. A 20–40B model at 4-bit quantization fits on the 24GB GPU that has been sitting in gaming PCs since 2022, delivering frontier-quality reasoning locally with no per-token bill and no data leaving the machine. Second, hallucination becomes a fixable bug instead of an unfixable property. When a fact lives in weights, a wrong answer has no address — you can't grep the weights or diff them against last month. When the fact lives in a document, a wrong claim has a source: you open it, edit it, and every future query gets the correction, with regression tests like any other data bug. There is a version of this future where model cards stop listing a knowledge cutoff entirely, because what's left in the weights goes stale on a scale of years instead of weeks. The model just gets handed the world's current state at runtime, the same way a CPU gets handed a program.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [w4g1.dev — Models Are Getting Dumber on Purpose](https://w4g1.dev/blog/models-are-getting-dumber-on-purpose) | HN Discussion: [264 points, 148 comments](https://news.ycombinator.com/item?id=49322695)*
