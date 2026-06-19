# Agent Memory Just Got a Production-Grade Blueprint — And It Runs on Elasticsearch

> Elasticsearch Labs built a persistent, multi-tenant agent memory layer with three cognitive indices, hybrid retrieval, and 0.89 R@10 recall. The architecture is open-source and the design decisions are worth studying.

## What Happened

On June 16, Elasticsearch Labs published a detailed technical write-up and open-source implementation of a persistent agent memory layer built entirely on Elasticsearch. The system achieves 0.89 R@10 recall across 168 evaluation questions with zero cross-tenant data leaks — and the reasoning behind every architectural choice is documented.

The core insight is that agent memory is not a single bucket. Drawing from cognitive science (the COALA framework), the team split memory into three Elasticsearch indices: **episodic** (raw user messages with timestamps), **semantic** (distilled, stable facts about the user), and **procedural** (multi-step playbooks with success/failure counters). Each index follows its own write rate, decay function, and update rules.

Retrieval uses a two-stage hybrid pipeline: BM25 for exact matches (version numbers, error codes, proper nouns) fused with Jina v5 dense vectors for semantic similarity via Reciprocal Rank Fusion (RRF), then re-ranked by a Jina v2 cross-encoder. The system handles contradictions through supersession rather than deletion — old facts are marked `superseded_by` rather than removed, preserving an audit trail while keeping the active index clean.

## Why It Matters

Context windows are not memory. A 1M-token context is a scratchpad for a single inference; it doesn’t survive session boundaries, doesn’t scale across months of interaction, and suffers from the well-documented “lost in the middle” effect where models ignore facts placed far from the prompt’s edges. Every production AI agent today — customer support, coding assistants, smart-home controllers — loses user context the moment the session ends. That’s not an implementation detail; it’s a fundamental limitation.

The Elasticsearch implementation addresses this with production-grade rigor. Same-turn write visibility is enforced through `refresh=True` on every write, ensuring the agent can recall a fact it just wrote within the same conversation turn. Per-user isolation uses Elasticsearch’s Document-Level Security (DLS), so Sarah’s memory is invisible to Bob’s queries. Time decay and use-count scoring prevent stale facts from outranking fresh ones. The consolidation pipeline runs an LLM over recent episodes to promote durable facts and playbooks, with deduplication guards.

The numbers matter: **R@10 0.89** across 168 questions means the system reliably surfaces the right memory in the top 10 results. **Zero cross-tenant leaks** means the multi-tenant security actually works. And the whole thing is one Elasticsearch cluster — not four separate services that can break independently.

## Developer Impact

If you’re building an AI agent that interacts with users across sessions, you need memory. The Elasticsearch implementation gives you a battle-tested architecture that you can deploy today: three indices, hybrid retrieval, supersession over deletion, and MCP-compatible tool interfaces. The code is open-source on GitHub.

The three-index cognitive model (episodic/semantic/procedural) is worth adopting regardless of your storage backend. Most agent builders default to “dump everything in a vector DB and hope,” which produces a haystack with no signal. Separating transient events from durable facts from procedural playbooks — and giving each its own lifecycle — is the difference between a memory system that works and one that drowns in its own history.

The supersession pattern is equally transferable. “Delete on contradiction” is tempting but dangerous; “supersede with audit trail” is the correct default for any system where facts evolve. Sarah moved from Bristol to Edinburgh — don’t delete Bristol, archive it and surface Edinburgh. Six months later, when she asks “where have I lived?”, both answers are correct in context.

## Our Take

Agent memory has been the elephant in the room for two years. Everyone knows context windows aren’t enough, but production implementations have been scarce. Elasticsearch’s blueprint changes that. Three indices, hybrid retrieval, supersession, and refresh-guaranteed write visibility — this is the reference architecture for anyone shipping persistent AI agents in 2026.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
