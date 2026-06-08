# Perplexity Just Rearchitected Search for AI Agents — Models Now Write Their Own Search Pipelines

> Search as Code lets AI models generate Python scripts that compose custom retrieval pipelines instead of calling fixed APIs. The result: 85% fewer tokens, 2.5× better performance, and a paradigm shift from "query → response" to "goal → orchestration."

## What Happened

On June 6, 2026, Perplexity unveiled **Search as Code (SaC)** — a new architecture that fundamentally changes how AI systems interact with search infrastructure.

Traditional search is simple: you send a query, you get back results. This works for humans reading 10 blue links. It breaks for AI agents running thousands of searches with domain-specific requirements. The agent loops through query → results → read → next query, burning tokens on irrelevant context and running serially when parallel work would be faster.

SaC inverts this. Instead of the model calling a search API, **the model writes Python code** that orchestrates Perplexity's search primitives. The code runs in a secure sandbox with an **Agentic Search SDK** that exposes search as composable building blocks: `search.web()`, `filter_by_domain()`, `dedupe_by_url()`, `rerank_by()`, and dozens more.

The three-layer architecture:

- **Control plane:** GPT-5.5 reasons about the task, designs the search strategy, and generates code
- **Execution layer:** A sandbox runs the generated Python with thousands of operations per minute
- **I/O layer:** The Agentic Search SDK provides atomic search primitives as SDK functions

## Why It Matters

This is not an incremental improvement. It's a category change in how models interact with external systems.

**First, context efficiency.** When a model calls a fixed search API, every result enters its context window — including noise. When the model writes code, filtering, deduplication, and restructuring happen in deterministic computation, not token space. In Perplexity's CVE research benchmark, SaC used **42.9K tokens vs. 288.7K** for the same task — an 85% reduction.

**Second, parallel execution.** Traditional search forces serial operation: call API, get results, think, call again. SaC lets the model fire off hundreds of queries in parallel, deduplicate in code, and only pass synthesized results back to the reasoning loop.

**Third, domain knowledge encoding.** The model can bake its understanding of specific domains directly into the search code. In the CVE example, the generated script knew Mozilla formats security advisories differently from Google, and generated vendor-specific query templates accordingly. Traditional search APIs can't capture this.

The benchmarks are striking. On WANDR (a complex multi-source research benchmark), SaC scored 0.386 vs. Anthropic's 0.152 — a **2.5× advantage**. Across all five benchmarks, SaC led or tied on four, and the cost-performance frontier was dominant at every reasoning level.

## Developer Impact

The most provocative implication: **generated code is becoming the universal interface** between AI models and external systems.

Function calling and MCP (Model Context Protocol) have been the standard for two years. But they're inherently limited — each call is a single-hop interaction, control flow lives in the model's reasoning loop, and state management is manual.

SaC shows a different path. When models generate code, they get:

- **Arbitrary control flow:** loops, conditionals, error handling — all in deterministic code
- **Efficient state management:** filesystem-based persistence with explicit serialization
- **Unlimited composability:** any primitive can combine with any other
- **Deterministic execution:** filtering, sorting, and verification happen at compute speed, not reasoning speed

Perplexity tested both REPL-style state (variables persist in memory) and filesystem-based serde. They chose filesystem + explicit serialization because **requiring models to explicitly manage state produced better results on long trajectories** — the explicit is better than implicit principle applied to AI-generated code.

This pattern will spread beyond search. Any API that an agent calls today could be replaced by an SDK that the agent programs against. The limiting factor is model code-generation quality — and that's improving fast.

## Our Take

Search as Code is one of those ideas that seems obvious in retrospect. Why would you call a black-box search API when you can write a program that defines exactly how to search, filter, and structure results? The answer, until recently, was that models couldn't generate production-quality code reliably. That constraint is gone.

The broader pattern — **code as the operational layer for AI** — will reshape integration architecture over the next 12 months. Function calling was the MVP. Generated code is the scalable abstraction. Perplexity just proved it works at production scale.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
