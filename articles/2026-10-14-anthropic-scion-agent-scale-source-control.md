# When 30,000 Merges a Day Broke Git: Inside Anthropic's Scion

> Anthropic's merge rate grew 12x in five months. Git became the bottleneck — so seven engineers replaced it in-house.

## What Happened

In May 2026, Anthropic merged roughly 2,500 code changes a day into its monorepo. By September it was merging over 30,000 — a 12x jump driven by Claude writing code at a rate no human team could match. The company had assumed coding was the hard part; it turned out that the *infrastructure built around human-scale coding* — source control, builds, CI — was the real ceiling.

The numbers explain why: the main branch's history alone is almost 100 GB and a working copy is over 25 GB. Anthropic performs on the order of 25 million code checkouts per day for CI alone; fetched naively, that would be more than 600 PB of git traffic daily. At its peak the team ran several hundred internal git mirrors, patching around git's limits with shallow clones, sparse checkouts, and blob-storage snapshots — tools that bought time but grew harder to operate as merges scaled. Worse, git's core assumption — that every client is a potentially offline, fully self-contained repository — was working against them, since nearly all of their clients are agents and jobs that are online by definition.

So a team of roughly seven engineers built **Scion**, an in-house version control system, plus a review surface (Antique), code search, and an IAM layer — and migrated the monorepo off git in five months. Scion inverts git's design: the server is always reachable and owns history, blame, commits, merges, and rebases, while clients keep only a bounded object cache and a sparse, on-demand working copy. Commits become "changesets" — write-ahead-log entries storing the changed file list relative to the first parent, plus content-addressed pointers to compressed blobs. There are no packfiles, no repository maintenance, and no fully materialized copy of the repo anywhere, even on the server. Derived data — file blame, path history, commit-graph skip lists — is computed recursively and can be built eagerly or deferred per type. Clients negotiate over trees and blobs instead of packfiles, riding a three-tier cache — per-node daemon, regional cache, authoritative servers — each holding a hit rate above 99%.

The results are stark: Scion's infrastructure footprint is 200x smaller than the previous git-mirror stack in CPU and memory, checkouts are two orders of magnitude faster (seconds instead of minutes), and CI alone reclaimed 25% of its CPU capacity.

## Why It Matters

This is one of the first concrete data points on what happens when autonomous agents, not humans, are the primary authors of a codebase. For decades the software lifecycle has been tuned to a developer who writes a few hundred lines and pushes a branch. When merges grow 12x, every downstream assumption — CI schedules, merge queues, review at human reading speed — becomes a bottleneck in turn. Anthropic states plainly that a proper merge queue no longer makes sense at agent scale: with hundreds or thousands of changes in flight, the industry-standard global repository lock is unreplicable, so the team invested in post-merge and pre-deploy testing and lets Claude resolve the resulting conflicts faster than a human could identify them.

It also reframes the developer's job. Anthropic says every engineer on the team would now rate Claude as better at writing code than they are — yet engineers remain essential for system-level judgment: choosing the right problems, and repeatedly telling Claude "no" when it over-engineers or violates a core constraint to satisfy a pattern. Reviewers now read the *intent* of a change rather than every line. A sharp example cuts both ways: an early hand-written git-to-Scion ingestion pipeline was rewritten by an internal Claude model to use 20x less memory and run 4x faster while preserving unstated invariants — yet much of the engineers' value lay in containing the same model's tendency toward excessive validation and complexity.

## Impact

Anthropic is explicit that Scion is not a git replacement, is not open source, and only makes sense for truly enormous monorepos — or an organization willing to spend unusual numbers of tokens internally. That is exactly why the post matters: it is an early warning, not a product launch. Anthropic expects this class of problem to reach ordinary teams within 6 to 12 months, and its recommendation points at the git ecosystem rather than at Scion — server-side architectures that differ from the git client, comprehensive server-side RPCs, and negotiation protocols that compose cleanly with sparse and shallow checkouts. GitHub and GitLab built their platforms for millions of human developers; the next decade of load will come from agent swarms. The infrastructure layer, not the model, is where that battle will be won or lost.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Anthropic Engineering](https://www.anthropic.com/engineering/redesigning-source-control) | HN Discussion: [543 points, 523 comments](https://news.ycombinator.com/item?id=50073633)*
