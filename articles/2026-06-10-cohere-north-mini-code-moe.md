# Cohere Just Made Enterprise-Grade AI Coding Open Source — and It Runs on 3B Active Parameters

**Date:** June 10, 2026  
**Tags:** #OpenSource #Cohere #MoE #Coding #Enterprise #Apache-2.0

---

## What Happened

Cohere released North Mini Code 1.0 on June 9 — a **30-billion parameter Mixture-of-Experts coding model** under the permissive **Apache 2.0 license**. The headline isn't the parameter count. It's that only **3 billion parameters are active at any given time** during inference, making this model dramatically cheaper to run than a dense 30B model would ever be.

This is Cohere's first open-source agentic coding model. It lands in a market already crowded with coding specialists — but Cohere's bet on enterprise pragmatism, permissive licensing, and hardware-efficient architecture makes the release strategically distinct.

## The Architecture

North Mini Code uses a sparse MoE design: 30 billion total parameters distributed across specialized expert networks, with a router that activates only the most relevant experts per token. The result is inference costs closer to a 3B model with the knowledge capacity of a 30B one.

Context window: **256K tokens**. Output ceiling: **64K tokens**. For perspective, 256K tokens can swallow an entire mid-sized codebase — every file, every function, every dependency — in a single prompt. The 64K output means the model can generate entire modules in one pass, rather than requiring developers to chain dozens of shorter completions together. This is purpose-built for agentic workflows where the model needs to plan, execute, and iterate across an entire codebase.

The model scored **27.6 on the Artificial Analysis Intelligence Index**, a standardized capability benchmark. This is below Cohere's own Command A+ (37), but closer inspection reveals the comparison is misleading — Command A+ is a general-purpose model, while North Mini Code is a coding specialist designed for a different cost-performance envelope.

## Apache 2.0: The Enterprise Unlock

The Apache 2.0 license is maximally permissive: companies can modify, deploy, fine-tune, and commercialize the model without any licensing friction. For enterprise CTOs at regulated institutions — banks, healthcare, defense — this is the difference between a model that legal approves and one that dies in compliance review.

Cohere's timing is sharp. The release comes less than three weeks after Command A+ (May 20), signaling an accelerating cadence. The company is clearly betting that enterprise AI adoption's next bottleneck isn't capability — it's deployability on owned hardware with air-gapped data.

## Context in the Market

This release sits at the intersection of three converging trends:

1. **The MoE efficiency wave.** DeepSeek V4, Qwen 3.5, and now North Mini Code all leverage sparse architectures to slash inference costs. The 10:1 ratio of total to active parameters is becoming the new normal for models that are meant to actually be deployed, not just benchmarked.

2. **The coding-model specialization race.** General-purpose models are getting better at code, but purpose-built coding models — with architecture tuned for long-context reasoning and structured output — are carving a separate niche. The question is whether this niche consolidates around 2-3 winners or fragments indefinitely.

3. **Open-source as enterprise strategy.** Cohere, Meta, DeepSeek, and Qwen are all betting that open-weight models capture enterprise adoption through deployability rather than raw benchmark scores. The 30B/3B split is the clearest articulation yet of this bet: make it small enough to run anywhere, open enough to adopt without lawyers, and specialized enough to actually do the job.

## Why It Matters

For small and mid-sized AI development companies — the kind that can't afford $10/M token API bills — North Mini Code represents a genuine path to running capable coding agents on-premises. The 3B active parameter requirement means a single high-end consumer GPU can serve this model.

The enterprise coding model market is bifurcating: premium closed-source models (Claude Code, GPT-5.5) compete on raw capability, while open-source MoE models compete on total cost of ownership. Cohere just raised the stakes in the second category — and with Apache 2.0, they've removed the last excuse for not deploying.
