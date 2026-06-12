# Microsoft Just Open-Sourced the Agent Framework It Uses in Production — And It's MIT Licensed

> Microsoft Agent Framework (MAF) is a production-grade, dual-language (Python + .NET) framework for building, orchestrating, and deploying multi-agent AI systems. With 11.3K stars, graph-based workflows, built-in observability, and Foundry hosting — it's the most complete agent infrastructure stack to go open-source in 2026.

## What Happened

On June 10, 2026, Microsoft released version 1.10 of its Agent Framework as a fully open-source MIT-licensed project on GitHub. Within days, it crossed 11,300 stars and 1,900 forks — making it the fastest-growing agent framework launch of the year.

MAF is not a research prototype. It's the same framework Microsoft uses internally for its own agent deployments, now available to anyone. The architecture covers the full agent lifecycle: building, orchestrating, deploying, and operating agent systems across both Python and .NET with consistent APIs and shared abstractions.

The framework ships with six production-grade capabilities that most open-source alternatives treat as afterthoughts:

**Graph-based workflow orchestration.** MAF supports sequential, concurrent, handoff, and group collaboration patterns out of the box. Workflows include checkpointing — you can pause, resume, and even "time-travel" through agent execution states.

**Built-in observability.** OpenTelemetry integration is native. Every agent call, tool invocation, and workflow transition emits distributed traces. No separate observability stack required.

**Middleware pipeline.** A flexible middleware system handles request/response processing, exception handling, and custom logic injection. Think Express.js middleware, but for agent calls.

**Declarative agent definitions.** Agents can be defined in YAML for version control, CI/CD integration, and team collaboration — no code required for basic agent setup.

**Foundry hosting.** Deploy agents to Azure Foundry's hosted infrastructure with two additional lines of code. Local dev to cloud production without rewriting orchestration logic.

**Multi-provider support.** MAF abstracts LLM providers behind a clean interface, supporting Azure OpenAI, OpenAI, Microsoft Foundry, and GitHub Copilot SDK — with more being added continuously.

## Why It Matters

The agent framework market has been fragmented. LangGraph dominates the Python ecosystem with 34.5M monthly downloads but feels academic. CrewAI is easy to start but hard to productionize. AutoGen's migration path is unclear. Semantic Kernel is powerful but .NET-only.

MAF solves the dual-language problem: Python for ML teams, .NET for enterprise engineering — with identical APIs and shared architecture decisions. The migration guides from both Semantic Kernel and AutoGen signal Microsoft's intent to consolidate its agent investments behind one framework.

More importantly, MAF treats durability and governance as first-class concerns. Checkpointing means long-running agent workflows survive crashes. Human-in-the-loop patterns are built in, not bolted on. The DevUI provides an interactive debugging environment for agent development — a tool that didn't exist in any major framework before.

The MIT license removes the last barrier. Enterprises that hesitated to build on frameworks with uncertain licensing or vendor lock-in now have a permissively-licensed, Microsoft-backed platform.

## Impact

MAF changes the competitive dynamics of the agent framework space in three ways:

**Consolidation pressure.** Python-only frameworks now face a competitor with equivalent Python support plus .NET. Teams with mixed-language stacks have one less reason to piece together multiple frameworks.

**Production bar raised.** By shipping checkpointing, OpenTelemetry, DevUI, and cloud hosting as built-in features, MAF resets what developers expect from an agent framework. Frameworks that treat these as "you figure it out" will struggle to compete.

**Enterprise acceleration.** The combination of MIT licensing, Microsoft backing, and Azure Foundry integration removes the three biggest blockers to enterprise agent adoption: legal uncertainty, vendor risk, and deployment complexity.

For the Chinese AI developer community, the dual-language design is particularly relevant. Python-dominant AI teams can build agents in their preferred language while .NET teams integrate them into existing enterprise infrastructure — without translation layers or interop glue.

The speed of adoption — 11.3K stars in a matter of days — suggests the market was waiting for exactly this. A framework that's open-source, production-ready, and backed by a company that runs agents at scale.
