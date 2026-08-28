# OpenExecutive: The Open-Source AI "C-Suite" That Turned a Layoff Story Into an Architecture Lesson

> After a CEO fired developers to "make room for AI," the developers built an open-source AI CEO — a single executive persona backed by eight specialist agents, with the engineering patterns worth studying on their own merits.

## What Happened

SenteLabsAI released **OpenExecutive** (Apache-2.0, 1.5k stars), an AI system that acts as a company's virtual executive team: a senior advisor with "Harvard MBA-level" knowledge, customized to a specific business. The HN headline frames it as irony — a CEO lays off developers to hire AI, and the developers ship an AI CEO as an open-source rebuttal. But underneath the satire is a complete, documented multi-agent reference architecture.

The system presents one coherent executive voice backed by **eight specialist agents**: Chief Strategy Officer, CFO, CHRO, General Counsel, COO, CMO, CPO, and a Board Communications Director. A user message hits an **Executive Orchestrator** (claude-sonnet-4-6), which routes to parallel specialist calls — with deep-reasoning agents (CSO, CFO, GC, Board) running on claude-opus-4-7 with extended thinking. Each specialist retrieves from two RAG layers in ChromaDB: git-tracked built-in MBA knowledge seeded at startup, plus chunked company documents in a separate collection.

The engineering details are unusually concrete. **Episodic memory**: after every response, a background claude-haiku-4-5 pass extracts decisions and initiatives into SQLite; the next session opens with a `<past_decisions>` block. **Scheduler**: a job runner claims due actions via `UPDATE ... RETURNING` to prevent double-firing, which forces a hard single-instance deployment constraint (documented explicitly in `fly.api.toml`). **Prompt caching**: the persona, company profile, and knowledge index are cached as separate blocks, reaching up to 85% cache-hit rate — with a strict rule that no dynamic content ever enters a cached block. **Evaluation**: 29 eval scenarios across all 8 domains, scored by claude-opus-4-7 as LLM-as-judge on five dimensions (persona coherence, domain accuracy, context utilization, routing quality, actionability), with a CI gate requiring ≥3.5/5 and no dimension dropping >10% vs main.

## Why It Matters

OpenExecutive matters less as a product than as a snapshot of where multi-agent orchestration has landed. The architecture encodes hard-won operational lessons: claim-based scheduling to survive concurrency, cache-partitioning discipline to keep costs sane, persistent memory layered under a stateless router, and evals wired into CI so agent behavior changes are regression-tested like code. These are the patterns any team building production agent systems is rediscovering — here consolidated into one readable, forkable codebase.

It also crystallizes a shift in what "executive work" is being automated. Much of organizational work is not the "IQ 180" strategic breakthrough but what operators call "token spewing": nudging, coordinating, tracking decisions, producing board decks. OpenExecutive targets exactly that layer — and the HN thread's 647 comments show how raw the debate is about which roles AI can absorb and who gets to build the replacement.

## Impact

For developers, the immediate value is the reference architecture: orchestrator-plus-specialists routing, dual-layer RAG, episodic memory in SQLite, a single-instance scheduler with atomic claim semantics, and LLM-as-judge evals as a CI gate — all under Apache-2.0 and swappable to local models via Ollama, LM Studio, or vLLM. Teams can lift these patterns directly into their own agent systems.

The strategic signal is broader: when a "virtual C-suite" can be assembled from open components and run on API keys, the marginal cost of organizational knowledge work collapses toward inference cost — the same curve that already reshaped coding. The open questions are governance, not capability: an AI GC or CFO giving confident-but-wrong advice has no liability shield, and single-instance schedulers are the least of the failure modes when autonomous agents start claiming real-world actions. The architecture is ready; the accountability layer is not.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [OpenExecutive GitHub Repository](https://github.com/SenteLabsAI/OpenExecutive) | HN Discussion: [938 points, 647 comments](https://news.ycombinator.com/item?id=49458418)*
