# ARD: The Discovery Layer AI Agents Have Been Missing

> A new open specification tackles the agent ecosystem's quietest bottleneck: how does an AI client discover which tools, servers, and capabilities are available to it?

## What Happened

The Agentic Resource Discovery Specification (ARD) has been released as an open standard under the Apache 2.0 license. Published by the ards-project on GitHub with 146 stars and counting, ARD defines a federated, domain-anchored protocol for cataloging, searching, and discovering agentic resources — MCP servers, A2A agent cards, Skills, APIs, and other callable services.

Built on top of the ai-catalog standard, ARD addresses a problem that has become impossible to ignore: the number of agentic resources is exploding. Some are public, some come from vendors, some are built inside companies. Some are narrow tools for a single task; others are full agents or workflows. The question ARD answers is deceptively simple: "What agentic resources can help with this task?"

The specification ships with CDDL schemas, JSON Schema definitions, and OpenAPI specifications — giving implementers everything they need to build compatible discovery services and clients. Hugging Face has already released `hf-discover`, an early reference implementation that demonstrates both client and server roles.

Critically, ARD only handles discovery — not invocation. Once a client finds a matching resource, it invokes that resource through the resource's own mechanism, whether that is MCP, A2A, a REST API, or an agent framework. ARD sits before invocation, helping the client decide which capability to reach for. This clean separation is deliberate: it means ARD can work alongside any existing agent protocol without requiring changes to how resources are actually called.

## Why It Matters

Today's agent ecosystem runs on static configuration. A developer or IT admin manually registers tools and servers with an agent client, and that registry becomes the agent's entire universe. This works for a handful of well-known tools. It breaks down when every product, team, and vendor is publishing agentic resources.

ARD turns discovery from a manual wiring problem into a queryable service. An agent can ask "what can help me with database schema migration?" and receive a ranked list of matching resources — what each does, who provides it, where it lives, and how to reach it. This transforms agents from closed systems with fixed capability sets into open systems that can dynamically discover and leverage new tools.

The federation model is particularly important for enterprises. An organization can run an internal ARD discovery service that catalogs approved vendor tools, internal APIs, and team-built agents. When a new employee's AI assistant needs to accomplish a task, it queries the discovery service and finds exactly the sanctioned resources — no manual onboarding, no stale configuration files.

## Developer Impact

For teams building agent systems, ARD eliminates a growing maintenance burden. Instead of updating tool registries across multiple agent clients whenever a new internal service or vendor API becomes available, you publish the resource to an ARD-compatible discovery service once. Every agent client that queries that service discovers it automatically.

The spec is invocation-agnostic by design. An MCP-native agent and an A2A-native agent can discover the same resource through the same discovery query, even though they invoke it through entirely different protocols. This is the kind of interoperability that the agent ecosystem desperately needs as it fragments across competing standards.

## Our Take

ARD is the DNS for AI agents — a federated lookup layer that transforms agents from isolated toolboxes into participants in an open capability ecosystem. At v0.9 draft, it is early but well-structured. If adoption follows the trajectory of MCP, this could become foundational agent infrastructure within a year.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
