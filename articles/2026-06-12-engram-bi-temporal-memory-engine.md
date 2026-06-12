# Less Context, Better Answers: A New Memory Engine Just Solved the Biggest LLM Agent Problem

> Engram, a bi-temporal memory engine from independent researcher Liuyin Wang, proves that a lean 9.6k-token retrieved context beats the full 79k-token history by 10.4 points on accuracy — at 8× lower cost. The system is open-source, self-hostable, and ships with the first reproducible memory benchmark harness in the field.

## What Happened

On June 5, independent researcher Liuyin Wang dropped a paper on arXiv titled "Less Context, More Accuracy: A Bi-Temporal Memory Engine for LLM Agents Where a Lean Retrieved Context Beats the Full History." The work addresses the fundamental tension in AI agent memory: you either replay the entire conversation history (expensive, slow, and accuracy-degrades as distractors accumulate) or you summarize (cheap, but loses critical detail). Engram proposes a third path — and backs it with hard numbers.

The core architecture is a dual-process system built on a bi-temporal data model. A fast write path appends lossless episodes without blocking on an LLM. An asynchronous consolidation path extracts atomic (subject, predicate, object) facts, builds a bi-temporal knowledge graph, and resolves contradictions — critically, without an LLM call per fact in the common case. A hybrid read path fuses five signals (dense, lexical, graph, recency, salience) and assembles a compact, provenance-tagged context.

The bi-temporal model is the deep innovation. Every fact carries two time axes: *valid time* (when the claim is true in the world) and *transaction time* (when the system learned or retracted it). When a fact is contradicted, it's invalidated, never deleted — preserving full provenance and a supersession chain. This makes knowledge-updates and "as-of" queries intrinsic rather than bolted-on.

## The Numbers That Matter

On the full 500-question LongMemEval benchmark, Engram's lean configuration scores **83.6%** versus **73.2%** for the full-context baseline — a +10.4 point gap at ~8× fewer tokens (9.6k vs 79k). McNemar's exact test puts p < 10⁻⁶. Across 500 paired questions, Engram is correct on 81 that the baseline misses, versus only 29 the other way.

The category breakdown reveals where the bi-temporal architecture earns its keep: knowledge-update hits 87.5% and temporal-reasoning 81.1%. The abstention gate — the system declines to answer when memory lacks evidence — reaches 86.7%. The remaining headroom is in multi-session aggregation (79.3%) and preference tasks (73.3%).

A load-bearing design finding: the read path must be hybrid. A facts-only path loses recall because extraction drops detail some questions need verbatim. Adding the most relevant raw session chunks back alongside conflict-resolved facts restores that specificity. The headline configuration ships both.

## Why It Matters

The AI memory field has been stuck in a false tradeoff: systems that win on cost lose on accuracy, and vice versa. Engram breaks this binary by showing that *removing distractors raises accuracy* — the filtered slice is not merely cheaper, it's more correct. This turns long-term memory from a cost optimization into a quality improvement.

The reproducibility contribution may matter more than the accuracy number. Engram ships with a neutral, in-repo evaluation harness — the official LongMemEval judge baked in, the full-context baseline in every table, raw per-question logs published. Wang documents the measurement-integrity pitfalls (truncation, home-grown judges, full-history "leaks") that silently distort benchmark numbers across sources. Every number in the paper ships with the command to reproduce it. In a field where the same system scores 58%, 66%, or 92% depending on who's measuring, the credible scoreboard is itself the result.

## Developer Impact

For teams building production AI agents, the implications are immediate and practical. The architecture runs fully locally with zero API keys via deterministic offline fallbacks — a hashing embedder, rule-based extractor, in-memory stores. Real backends (BGE embeddings, LanceDB/Qdrant, Kuzu/Neo4j, any LLM via LiteLLM) slot in behind the same interfaces.

The dual-license (AGPL-3.0 + commercial) means you can self-host or buy a license. The cost profile is flat as history grows — the retrieved slice stays bounded around 10k tokens regardless of how many sessions accumulate, while full-context scales linearly.

The design lessons are transferable even if you don't adopt Engram directly: bi-temporal modeling for knowledge updates, non-destructive conflict resolution with provenance chains, hybrid facts-plus-chunks retrieval, and an abstention gate for when memory genuinely lacks the answer. These are the patterns that turn agent memory from a hack into infrastructure.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
