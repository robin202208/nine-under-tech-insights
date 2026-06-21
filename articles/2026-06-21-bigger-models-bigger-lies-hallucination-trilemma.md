# Bigger Models, Bigger Lies: The Hallucination Trilemma Nobody's Solving

> A 753B-parameter open-weight model just embarrassed GPT-5.5 and DeepSeek V4 Pro on truthfulness — exposing the dirty secret of the scaling era: intelligence plateaus while confidence skyrockets.

## What Happened

A new benchmarking study published by Oliver Shrimpton at arrowtsx.dev has produced the most uncomfortable AI truthfulness data of 2026. The headline: GPT-5.5 hallucinates at 3x the rate of GLM-5.2, Z.ai's 753B-parameter MIT-licensed model that activates only ~40B parameters per inference.

On the Artificial Analysis Omniscience benchmark, the numbers are stark. DeepSeek V4 Pro — a 1.6-trillion-parameter behemoth — achieved a hallucination rate of 94%. That means on questions it couldn't answer, it admitted ignorance only 6% of the time. The remaining 94%: confidently fabricated answers with full reasoning chains. GPT-5.5 scored 68%. Fable 5 hit 48%. GLM-5.2 landed at 28%.

The study's most damning evidence came from a controlled Python architecture question. Asked to design a custom asyncio event loop that overrides `get_child_watcher()` without using `asyncio.create_task` or raw `select/poll`, DeepSeek V4 Pro spent 3 minutes and 26 seconds in a reasoning loop, consumed an estimated 10x the reasoning tokens of GLM-5.2, and produced a beautifully structured, confidently incorrect solution. GLM-5.2 took 12 seconds and 800 reasoning tokens to recognize the technical impossibility — you cannot multiplex I/O on a single thread without yielding or polling.

## Why It Matters

This isn't just another "small model beats big model" story. It exposes a structural problem in how we train frontier models: massive parameter counts, trained on massive corpora, learn that *having an answer* is always better than *not having one*. The model never gets rewarded for saying "I don't know" during training — there is no "I don't know" token in the loss function.

Shrimpton frames this as the "unsolved trilemma of modern LLMs": you cannot simultaneously optimize for raw capability, uncertainty calibration (hallucination rate), and computational efficiency. Pick two. The biggest labs — OpenAI, DeepSeek, Anthropic — have all chosen capability and are paying the calibration price in hallucination debt.

The irony is that this was predictable. The same dynamic exists in human expertise: the most knowledgeable people are often the most overconfident in domains adjacent to their expertise. Frontier models have become the AI equivalent of the Dunning-Kruger effect at trillion-parameter scale.

## Developer Impact

For builders, the implication is immediate and uncomfortable: **model selection cannot be based on benchmark leaderboards alone.** The AA Intelligence Index places GPT-5.5 just 4 points above GLM-5.2, but the hallucination gap is 3x. If you're building an agent that needs to make API calls based on model output, hallucination rate matters far more than a 4-point benchmark delta.

The practical takeaway: GLM-5.2's open-weight, MIT-licensed availability combined with its industry-best calibration makes it the strongest candidate for production agentic systems where factual reliability outweighs creative fluency. And for the labs: training objectives need to incorporate uncertainty signals — whether through RLHF that rewards refusal on unknowable questions, or architectural changes that decouple confidence from generation.

## Our Take

The scaling era's last believers are running out of intellectual cover. When a 753B-parameter model that activates only 5% of its weights can match frontier models on intelligence while beating them 3x on truthfulness, the "just make it bigger" narrative collapses. The next frontier isn't parameter count — it's honesty.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
