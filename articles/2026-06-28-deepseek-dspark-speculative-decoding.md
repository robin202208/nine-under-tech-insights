# DeepSeek Just Open-Sourced the Inference Speedup Every LLM Provider Needs

**June 28, 2026** · DeepSeek · LLM Inference · Open Source

---

## What

DeepSeek released **DeepSpec**, a full-stack open-source codebase for speculative decoding, alongside the **DSpark** paper. Speculative decoding is a technique where a small "draft" model guesses tokens ahead, and the main model verifies them in parallel — turning sequential token generation into batch verification. If the draft guesses right, you get multiple tokens per forward pass instead of one.

The repo doesn't just describe the idea. It ships everything: data preparation pipelines that regenerate target-model answers and build 38TB caches, three draft model implementations (DSpark, DFlash, Eagle3), training scripts configured for single-node 8-GPU setups, and evaluation harnesses across nine benchmarks — GSM8K, MATH500, AIME25, HumanEval, MBPP, LiveCodeBench, MT-Bench, Alpaca, and Arena-Hard-v2. Target models include Qwen3 (1.5B–14B) and Gemma 3 (1B–12B). Everything is MIT-licensed.

The paper reports **2.2–3.1× inference speedup** across model sizes with zero quality degradation. DSpark achieves this by training draft models that are architecturally aligned with their target, learning to predict the target's behavior rather than generic next tokens. The draft is small enough to run fast but accurate enough that the target accepts most of its guesses on the first try.

---

## Why

Inference speed is the bottleneck that decides whether AI products are viable. Every 100ms of latency costs users. Every extra GPU eats margin. Speculative decoding has been known since Google's 2022 paper — but productionizing it required solving three hard problems.

First, **acceptance rate**. A draft model that the target keeps rejecting is worse than useless; it wastes compute on discarded tokens. DeepSeek's approach trains drafts specifically to match the target's distribution, achieving high acceptance rates that translate directly to wall-clock speedups.

Second, **data scale**. Training requires regenerating every answer through the target model to build a "target cache" — 38TB for Qwen3-4B alone. The repo provides the full pipeline to handle this.

Third, **reproducibility**. Most speculative decoding implementations are research artifacts — they work in a paper but not in production. DeepSpec ships training configs, evaluation scripts, and benchmark datasets. You can reproduce the results or adapt them to your own models.

The timing matters. Anthropic's export restrictions on Mythos-class models have pushed Asian AI labs toward aggressive open-source strategies. But DeepSeek's play is smarter than just shipping model weights: they're open-sourcing the **infrastructure** that makes models cheaper to serve. As one HN commenter noted: "by sharing not only models but serving optimizations, even third parties serving DeepSeek models can do so more efficiently, multiplying the effect on western competitors' margins."

This is a strategic masterstroke. The AI race isn't just about who has the best model — it's about who controls the cost structure. By making inference optimization a commodity, DeepSeek shifts the battleground to application quality, where ecosystem size matters more than model capability.

---

## Impact

**For AI application developers:** If you're running Qwen or Gemma in production, DSpark is a free 2–3× throughput improvement. The cost math is simple: same outputs, fewer GPUs, lower latency. The training recipes are ready to run.

**For the open-source ecosystem:** DeepSpec joins DeepSeek's growing infrastructure arsenal — FlashMLA, DeepGEMM, 3FS — each lowering the barrier to running competitive AI without hyperscaler budgets. Google invented speculative decoding; DeepSeek built the factory.

**For the competitive landscape:** This is how open-source wins — not with a single best model, but by making the entire stack accessible. When inference optimization is table stakes, advantage shifts to whoever builds the best applications. The 719 HN points and 298 comments confirm: developers know inference efficiency is the next battleground, and DeepSeek just handed out weapons.

---

*Read the paper: [DSpark: Speculative decoding accelerates LLM inference](https://github.com/deepseek-ai/DeepSpec/blob/main/DSpark_paper.pdf)*  
*Code: [github.com/deepseek-ai/DeepSpec](https://github.com/deepseek-ai/DeepSpec)*
