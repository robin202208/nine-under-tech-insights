# Grok 4.6 Rejoins the Frontier at a Fraction of the Price — And It Wins on Turns, Not Just Scores

> SpaceXAI's latest model matches GPT-5.6 Sol on the Artificial Analysis Intelligence Index while charging a fraction of the price — and its biggest advantage is how few tokens it burns to finish long agentic tasks.

## What Happened

On August 12, SpaceXAI released Grok 4.6, a successor to Grok 4.5 built specifically around long-running agents and ambitious interactive and visual work — researching unfamiliar domains, restructuring applications, and refining outputs through multiple feedback rounds. The model is available immediately in Cursor, Grok Build, the xAI API, and through partners including OpenRouter, Vercel, and Cloudflare, at unchanged pricing of $2 per million input tokens and $6 per million output tokens.

The training recipe differs meaningfully from its predecessor. Grok 4.6 underwent a longer supplemental training run with curated model-generated data for reasoning and advanced technical concepts, plus an improved optimizer and training recipe. SpaceXAI then used Grok 4.5 to regenerate SFT trajectories across reasoning efforts, agent harnesses, STEM, software engineering, and knowledge work, filtering out problematic traces with model-based checks. The subsequent RL stage covered a wide range of agentic tasks, including specialized environments for kernel optimization, web development, and computer-aided design.

Independent evaluation confirms the release is not incremental hype. Grok 4.6 scores 61 on the Artificial Analysis Intelligence Index, up 5 points from Grok 4.5 and 23 points from Grok 4.3 — matching GPT-5.6 Sol and trailing only Claude Opus 5 (63) and Claude Fable 5 (62). Its strongest results are in agentic work: a GDPval-AA v2 Elo of 1753, behind only Claude Opus 5; a top-two score of 50.7% on the τ³-Banking multi-turn customer-service benchmark; and 88.4% on Terminal-Bench v2.1, level with the leaders.

## Why It Matters

The first remarkable fact is that a frontier-scoring model shipped at the same price as its predecessor. Holding headline pricing flat across a generation is unusual at the frontier, where intelligence gains have historically been accompanied by price increases. At $2/$6 per million tokens, Grok 4.6 undercuts Claude Opus 5 ($5/$25) and GPT-5.6 Sol ($5/$30) by 60% or more on output tokens — the dimension that dominates cost in reasoning-heavy workloads. Artificial Analysis measured it at $0.84 per task, placing it on the Intelligence vs. Cost-per-Task Pareto frontier.

The second, subtler fact is about efficiency, not price. On AA-Briefcase, Artificial Analysis's private benchmark of long-horizon agentic knowledge work, Grok 4.6 scores an Elo of 1577 — Fable 5-tier, behind the Claude Opus 5 family — but reaches its answers in roughly half the turns and a quarter of the input tokens: ~53 turns and ~0.5B input tokens on average, versus ~103 turns and ~2.0B for Claude Opus 5 (max). Long-horizon agentic work accumulates context rapidly, so this efficiency compounds into a cost advantage well beyond per-token pricing, especially since Grok 4.6's cache-hit discount was raised to $0.50 per million tokens.

This positions Grok 4.6 as a genuinely different kind of frontier release: not the highest raw score, but the strongest score-to-cost ratio in the top tier, backed by evidence that it holds up on exactly the workloads agents actually run — knowledge work, customer service, and terminal use simultaneously.

## Impact

For developers building agentic systems, Grok 4.6 changes the default economic calculation. The models within two points of it on the Intelligence Index cost three to five times more per token, and frontier-level agent work has historically been the most expensive category of AI usage. A model that matches the leader on composite intelligence, trails only narrowly on long-horizon work, and does so with materially better turn efficiency makes frontier capability accessible for high-volume, multi-step workloads that would previously have been cost-prohibitive.

The release also signals that the competitive frontier has shifted from raw capability to capability-per-dollar and capability-per-turn. SpaceXAI's choice to fund a longer training run and reinvest in agentic RL environments — rather than scaling parameters — mirrors a broader industry pattern where post-training and agent-specific reinforcement learning now move the needle more than raw scale. For buyers, the practical implication is that benchmark scores alone no longer tell the story: two models with the same Intelligence Index can differ by 4× in input tokens consumed on the same task, and that gap is the one that hits the invoice.

Expect pricing pressure on the top tier as a result. When a frontier-scoring model sells at $6 per million output tokens, incumbents charging $25–$30 face a harder conversation with procurement — and the efficiency data from independent evaluations gives buyers a concrete basis to demand more for less.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [SpaceXAI Blog](https://x.ai/news/grok-4-6) & [Artificial Analysis](https://artificialanalysis.ai/articles/grok-4-6-benchmarks-and-analysis) | HN Discussion: [398 points, 396 comments](https://news.ycombinator.com/item?id=49274027)*
