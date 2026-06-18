# GLM-5.2 Dethrones Every Open Weights Model — And Matches GPT-5.5 on Agentic Tasks

Zhipu AI's GLM-5.2 is now the highest-scoring open weights model on the Artificial Analysis Intelligence Index, scoring 51 points — a full 7 points ahead of MiniMax-M3 (44) and DeepSeek V4 Pro (44). More strikingly, on GDPval-AA v2, the benchmark that measures real-world agentic performance, GLM-5.2 scores 1524, statistically tied with OpenAI's GPT-5.5 (xhigh reasoning, 1514). An MIT-licensed Chinese model is now competing in the same tier as the most expensive proprietary frontier systems.

## What Happened

GLM-5.2 maintains the same architecture as GLM-5.1 — 744 billion total parameters with 40 billion active via Mixture of Experts — but scores 11 points higher on the Intelligence Index. The gains are concentrated in scientific reasoning: CritPt (+16 points), HLE (+12 points), and SciCode (+7 points). TerminalBench v2.1, which measures agentic coding ability, jumped 16 points to 78%.

The context window doubled from 200K to 1 million tokens. Pricing remains unchanged at \.40/\/bin/bash.26/\.40 per million input/cache-hit/output tokens. The model sits on the Pareto frontier of Intelligence vs. Cost per Task at roughly \/bin/bash.46 per task, making it the most cost-effective model at its capability level.

GLM-5.2 is available not only through Zhipu's first-party API but across a wide range of third-party providers: DeepInfra, Novita, Nebius, Parasail, SiliconFlow, GMI Cloud, Baseten, and Fireworks. The license is MIT — fully permissive for commercial use, modification, and redistribution.

One notable trade-off: GLM-5.2 uses 43,000 output tokens per Intelligence Index task, up from 26,000 for GLM-5.1 and above peers like MiniMax-M3 (24K) and Kimi K2.6 (35K). It is among the less token-efficient open models, which raises the per-task cost despite competitive per-token pricing.

## Why It Matters

This release marks an inflection point for open weights AI. For the first time, an open model is not just "competitive with" but clearly ahead of every other open model — and effectively equivalent to the best proprietary systems on agentic benchmarks. This challenges the narrative that frontier AI capability requires closed, API-gated access.

The GDPval-AA v2 result is particularly significant. Unlike knowledge-recall benchmarks such as MMLU, GDPval-AA measures performance on long-horizon agent trajectories: a model must plan, execute multi-step tasks, use tools, and recover from errors over up to 250 turns. That GLM-5.2 matches GPT-5.5 here suggests the agent capability gap between open and closed models has effectively closed.

The MIT license is strategic. Zhipu is betting that widespread adoption — the model is already integrated into eight third-party inference providers — will generate more value than API lock-in. This mirrors Meta's playbook with Llama, but with a model that is actually leading the leaderboard rather than playing catch-up.

## The Impact

**Short-term**: Teams building AI agents now have a genuinely frontier-grade open model they can self-host or run through competitive providers. The 1M context window makes it suitable for long-document processing, codebase analysis, and extended agent sessions. Expect rapid integration into agent frameworks like LangChain, CrewAI, and Browser-Use.

**Medium-term**: The open weights frontier race intensifies. MiniMax, DeepSeek, Kimi, and others will need to respond. Competition among Chinese AI labs — already fierce — will accelerate, likely producing even stronger open models within months. Western labs may face pressure to release more capable open weights or risk losing the developer ecosystem.

**Long-term**: If open models can maintain parity with proprietary systems on agentic tasks, the economic center of gravity in AI shifts from model providers to infrastructure and application layers. Companies building on top of models gain bargaining power; model providers become commodities faster than expected. GLM-5.2 is not just a benchmark result — it is a preview of that future.

---

*Published: 2026-06-18 | Source: [Artificial Analysis](https://artificialanalysis.ai/articles/glm-5-2-is-the-new-leading-open-weights-model-on-the-artificial-analysis-intelligence-index) | HN Discussion: [780 points](https://news.ycombinator.com/item?id=46407558)*
