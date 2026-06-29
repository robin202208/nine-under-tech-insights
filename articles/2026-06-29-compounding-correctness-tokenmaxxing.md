# Compounding Correctness: When More Tokens Mean Better Code, Not Just More Spend

> AI models have crossed a threshold where spending extra tokens makes outcomes better, not worse — flipping the economics of agentic software development.

## What Happened

For months, "tokenmaxxing" was a punchline. Companies like Meta tied employee performance reviews to token usage, resulting in developers running two AI agents talking to each other all day just to hit their numbers. The consensus was clear: encouraging token spend without measuring output was a classic executive blunder — burning money for nothing.

That story has now inverted. According to a detailed analysis from the Agentics community, the underlying technology has changed in a fundamental way. We've moved from "compounding error" to "compounding correctness." Previously, running an AI agent unsupervised for hours meant small hallucinations would snowball into irrecoverable messes. There was no point running agents overnight — they'd just tear up your work. But today's models, harnessed by mature frameworks like Claude Code and its recently announced "loops" feature, actually get better the longer they run.

The turning point is visible in the numbers. The UK's AI Safety Institute (AISI) recently tested Anthropic's Mythos — a model so capable at cybersecurity that Anthropic restricted access to critical software makers — with a 100-million-token budget per attempt, costing $12,500 per run. Across all ten attempts, the model showed zero signs of diminishing returns. More tokens consistently meant more exploits found. The security equation reduces to a brutal simplicity: to harden your system, you must spend more tokens than your attackers.

## Why It Matters

This reversal matters because it changes what "efficient" means in AI-assisted development. The old regime punished token waste. The new regime rewards token investment — provided you're running the right kind of workload. Boris Cherny, creator of Claude Code, demonstrated "loops": restart the same prompt after completion, let the agent split large tasks into parts, and solve them over time with zero human supervision. The concept has existed since mid-2025 (originally called a "Ralph Wiggum loop"), but it only recently became reliable because models stopped degrading over long runs.

The economics are equally significant for the open vs. closed model debate. If Claude Opus gives you 1.1x improvement per loop iteration, and GLM 5.2 or another open model gives you 1.05x improvement but costs 5x less, you can simply run the cheaper model more times and get a better result overall. Tokenmaxxing becomes a rational strategy — but only if you're winning on price-per-quality-improvement, not on raw spend.

## Impact

The implications for development teams are immediate. Those building "software factories" — automated pipelines where agents write, review, fix, and test code without human intervention — are already seeing the shift. The natural end state is the "dark factory": engineers input specifications, and the entire application lifecycle runs autonomously.

But there's a hazard. The article distinguishes between two flavors of tokenmaxxing. One is productive: developers using tools like Claude Code in loops to multiply their own output. The other is wasteful: hand-coded, one-off agent pipelines built by consultants that guzzle tokens but never work reliably. The former compounds correctness; the latter compounds cost. As generalist agent platforms become powerful enough to replace brittle bespoke pipelines, the market arbitrage will close — but not before a lot of money gets spent on the wrong kind of tokens.

For individual developers, the message is clear: if you haven't experimented with agent loops yet, now is the time. The technology has crossed a reliability threshold. Spending a few hundred dollars in API tokens per month — less than a typical SaaS subscription — can genuinely amplify your output. The era of treating token spend as waste is over. The era of treating it as investment has begun.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Agentics / Tech Things](https://12gramsofcarbon.com/p/agentics-tech-things-tokenmaxxing) | HN Discussion: [108 points, 135 comments](https://news.ycombinator.com/item?id=48708795)*