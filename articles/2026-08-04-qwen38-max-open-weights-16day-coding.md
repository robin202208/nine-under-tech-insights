# Qwen 3.8-Max: Alibaba's First Open-Weight Flagship Just Ran a 16-Day Coding Project With No Human in the Loop

> Alibaba shipped its most capable model ever, promised open weights for the first time in the Max tier, and proved the point with a two-week autonomous coding run whose full trace is public.

## What Happened

On August 3, 2026, Alibaba's Qwen team made Qwen3.8-Max generally available on Alibaba Cloud Model Studio. It is the largest model in the Qwen family: 2.4 trillion total parameters in a sparse Mixture-of-Experts design built on the Qwen 3.5 architecture, activating roughly 95 billion per forward pass — about 4% of the total. The spec sheet reads like a frontier checklist: 1M-token context, text/image/video input, 131K max output tokens, `reasoning_effort` controls, and flat pricing of $2 per million input tokens and $6 per million output, with cached input at $0.25/M.

Two strategic firsts make this release bigger than the spec sheet. It is the first Qwen-Max-class model ever promised open weights — Alibaba says the checkpoint lands on Hugging Face and ModelScope next week, alongside a smaller Qwen3.8-27B. And it debuts QwenWork, Alibaba's answer to the suddenly crowded "cowork" platform space occupied by Claude Cowork, ChatGPT Work, and Kimi Work.

The benchmark table (all vendor-run; no independent verification exists yet) shows genuine wins and honest losses. Qwen3.8-Max leads PaperBench at 93.0 — a 28-point jump over Qwen3.7-Max — and IFBench at 82.8, ten points ahead of GPT-5.6 Sol. It edges Claude Opus 4.8 and Fable 5 on Terminal-Bench 2.1 (86.6 vs 84.6) but trails Fable 5 by 12 points on SWE-bench Pro and 15 on FrontierSWE. The largest generational gains are in agentic and multimodal rows: DeepSWE 1.1 jumps from 21.6 to 56.6, JobBench from 31.3 to 53.4, OSWorld-Verified lands at 86.1.

But the story Alibaba really wants told is not the table — it is the demo. Tasked with building a self-evolving CLI framework from an empty repo, Qwen3.8-Max ran autonomously for 16 days: 265 commits, 127 PRs, 151 issues, all opened and closed by the model, with the entire trace public at `qwen-code-dev-bot/oh-my-cli`. In a second test it reproduced a published LLM research paper from scratch in 125 hours, confirmed all six findings, then beat the paper's AIME24 score by 2.7 points with its own ideas. In a third, it climbed ahead of 458 of 526 human teams on a Tianchi competition leaderboard.

## Why It Matters

Two structural shifts are buried in this release, and both outlast any single benchmark row.

First, open-weight Max changes the competitive map. For three generations Alibaba's flagship tier stayed closed behind QwenCloud while the open ecosystem caught up with smaller models. Promising Max weights is a bet that open distribution now captures more developer mindshare — and downstream, more paid API usage and Alibaba Cloud workloads — than exclusivity does. It is the same logic that pushed DeepSeek and Kimi K3 into the open-weight frontier, and it confirms the "open source won" narrative now reaches the very top of the model market. Caveats are real: the license is undefined, the weights aren't downloadable, and a 2.4T checkpoint is a multi-node datacenter artifact — the 27B model is the realistic self-host path.

Second, the 16-day run reframes what a coding benchmark should measure. Terminal-Bench and SWE-bench score bounded tasks; oh-my-cli tested error recovery, context drift, and self-correction across two weeks of continuous operation — the failure modes that decide whether agentic coding is production-credible. Tellingly, Alibaba ran much of its evaluation with the Claude Code harness and published the config: the field has converged on the same scaffolding, so vendors now compete on what the model does inside it.

Honesty check: every number is vendor-run, the multimodal table compares against Qwen3.7-Plus rather than Max (flattering the generational delta), the 95B active figure lacks independent confirmation, and Qwen's own RL scaling curve peaks near 4,000 training environments then declines.

## Impact

For developers, the moves are immediate: the API is OpenAI- and Anthropic-compatible, so integration is a base-URL swap; 1M context at $2/$6 is aggressive for long-document and repository-scale work; and the oh-my-cli trace is a public, auditable artifact for studying what long-horizon autonomy actually produces. When the weights land next week, a frontier-class MoE becomes inspectable — and pressure on every closed flagship to follow grows.

The deeper signal is that the center of gravity in agentic AI is shifting from "can a model answer" to "can a model be left alone with a repository and a milestone." Qwen3.8-Max is not the first model to claim it, but it is the first to ship the receipts — and the first to pair the claim with open weights.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Qwen Official Blog](https://qwen.ai/blog?id=qwen3.8) & [Alibaba Cloud Community](https://www.alibabacloud.com/blog/alibaba-unveils-qwen3-8-max-its-largest-and-most-capable-flagship-model-to-date_603420) | HN Discussion: [1051 points, 565 comments](https://news.ycombinator.com/item?id=49150470)*
