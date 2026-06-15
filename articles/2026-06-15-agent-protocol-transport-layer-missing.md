# The AI Agent Protocol Stack Is Missing a Layer — And Nobody Is Talking About It

**Date:** 2026-06-15  
**Category:** AI Infrastructure / Agent Architecture  
**Source:** VentureBeat

---

## What Happened

In the past eighteen months, four major AI agent protocols have been published: Anthropic's Model Context Protocol (MCP) for tool calling, Google's Agent2Agent (A2A) for task coordination, IBM's Agent Communication Protocol (ACP) for lightweight messaging, and the Agent Network Protocol (ANP) for decentralized identity and discovery. Together they form an emerging stack: ANP for discovery → A2A for coordination → MCP for tool execution → ACP for simple message passing.

Ten thousand active public MCP servers. A hundred sixty-four million monthly Python SDK downloads. Google donated A2A to the Linux Foundation. The W3C opened an AI Agent Protocol Community Group. The IETF is receiving Internet-Drafts on agent transport. Every week brings a new GitHub repository claiming to solve agent communication.

The stack is maturing fast — but it has a hole. Every protocol listed runs over HTTP.

## Why HTTP Is the Wrong Foundation

HTTP assumes a reachable server. Behind NAT — and 88% of networked devices sit behind NAT — there is no reachable server without a relay. For agent fleets that need to route tasks directly between peers across cloud boundaries, home networks, and edge deployments, HTTP forces every message through centralized relay infrastructure. Relay infrastructure adds latency, cost, and a single failure domain.

The application-layer protocols solve the semantics of what agents say to each other. They do not solve how agents find each other and establish direct connections. That is a session-layer problem — Layer 5 in the OSI model — and none of MCP, A2A, ACP, or ANP address it.

This isn't an unsolved computer science problem. The primitives exist: UDP hole-punching with STUN provides NAT traversal for roughly 70% of network topologies. X25519 Diffie-Hellman and AES-256-GCM provide authenticated encryption at the tunnel level without certificate authorities. QUIC (RFC 9000) provides reliable delivery without TCP's head-of-line blocking. These are the same primitives that WireGuard uses for VPN tunnels and that WebRTC uses for browser-to-browser media streams.

## Capability-Based Routing Changes the Game

The real innovation needed isn't just a transport protocol — it's a routing paradigm. Agents need to find peers not by hostname or IP address, but by what those peers can do. A research agent should be able to query "which peers have real-time foreign exchange data?" and receive a list of currently active specialist agents.

This is closer to a service registry than to DNS. It demands a dynamic, capability-aware discovery mechanism paired with direct peer-to-peer connections. ANP's JSON-LD capability descriptions and decentralized identity primitives are the natural starting point, but the transport layer that connects two peers after discovery doesn't exist yet.

The irony is that while MCP and A2A have already attracted tens of millions in enterprise investment and dedicated Linux Foundation working groups, the transport problem — the layer that determines whether agents can actually talk to each other in production — remains an afterthought. Protocol designers built for demo environments where every agent has a public IP and a TLS certificate. Production environments are messier.

## Why This Matters Now

The window for getting the transport layer right is narrow. Once MCP and A2A harden into standards and enterprises deploy HTTP-based agent infrastructure at scale, retrofitting peer-to-peer transport becomes exponentially harder. The history of distributed computing is one of transport decisions made early that proved impossible to unwind — CORBA's IIOP, SOAP's HTTP binding, and even REST's limitations under real-time streaming workloads all demonstrate the cost of getting the transport abstraction wrong at standardization time.

The agent ecosystem is still young enough to fix this. The W3C Community Group and IETF drafts are in early stages. The protocol authors are actively collaborating rather than competing — A2A explicitly delegates tool execution to MCP, and ANP's design philosophy complements rather than conflicts with either. The missing piece is a session-layer specification that enables direct, authenticated, capability-routed peer connections between agents regardless of network topology.

Whoever ships that layer — whether as an extension to ANP, a new IETF standard, or an open-source implementation that becomes the de facto standard — will define how AI agents communicate for the next decade. The race is on.

---

*VentureBeat: [MCP solved tool calling. A2A solved coordination. What solves transport?](https://venturebeat.com/ai/mcp-solved-tool-calling-a2a-solved-coordination-what-solves-transport/)*
