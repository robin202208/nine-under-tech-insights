# AgentJacking: MCP Just Became the New Supply Chain Attack Vector

> A single fake Sentry bug report hijacked AI coding agents at a $250B company—and 100+ more. No breach required. Just a public DSN and an HTTP POST.

## What Happened

Tenet Security's Threat Labs just demonstrated what they're calling **"AgentJacking"** — a new attack class that hijacks Claude Code, Cursor, and Codex through their MCP (Model Context Protocol) integrations. The mechanism is terrifyingly simple:

1. **Find a Sentry DSN.** These are public, write-only credentials embedded in every website's frontend JavaScript. Tenet found 2,388 organizations with exposed DSNs — 71 in the Tranco top-1M.

2. **POST a crafted error event.** Sentry's ingest API accepts arbitrary payloads from anyone with the DSN. No authentication beyond that public key. The event payload includes markdown-formatted instructions disguised as a legitimate "Resolution" section — indistinguishable from Sentry's own diagnostic templates.

3. **The agent reads it and executes.** When a developer asks their AI agent to investigate Sentry errors, the agent queries Sentry via MCP, receives the injected event, and interprets the attacker's instructions as authoritative guidance. It runs `npx @attacker-package --diagnose` with the developer's own privileges.

**The result:** 85% exploitation success rate across all tested agents. 100+ confirmed hijacks across six continents. One victim: a Fortune 100 technology company worth $250 billion. Sectors ranged from finance and healthcare to government and critical infrastructure.

Sentry acknowledged the disclosure on the same day — but **declined to fix it at the root**, calling it "technically not defensible" at the platform level. They added a content filter for the specific payload string and moved on.

## Why It Matters

This isn't just another prompt injection paper. AgentJacking reveals a structural flaw in the agentic AI supply chain that no one has governance for:

**Every MCP-connected service is now an injection surface.** MCP was designed as a standardized integration layer between AI agents and external tools. It treats tool responses as authoritative data. But it has no concept of data provenance — no mechanism to distinguish "this error came from our application" from "this error was POSTed by an attacker with a public DSN."

The attack bypasses every conventional security control. EDR sees a trusted process executing a legitimate npm command. WAF sees routine outbound traffic. IAM confirms the developer was authorized. No policy is violated. No anomaly threshold is crossed. Tenet calls this the **"Authorized Intent Chain"** — a sequence of actions that are individually authorized but collectively catastrophic.

**Sentry isn't the only vector.** Any MCP-connected service that surfaces externally-controlled content creates the same vulnerability class. Issue trackers. Ticketing systems. Code review platforms. Log aggregators. Customer support queues. The attack surface grows with every MCP server deployment.

**You can't prompt-engineer your way out.** Tenet tried. They added explicit system prompts telling agents to ignore untrusted data. The agents executed the payload anyway. The models cannot reliably distinguish between data and instructions when both arrive through trusted tool channels — this is a limitation of the architecture, not a misconfiguration.

**The blast radius is enormous.** From one foothold, agents had access to AWS keys, GitHub OAuth tokens, SSH agent sockets, Docker configs, Kubernetes tokens, CI/CD pipeline secrets, VPN credentials, and internal cloud hostnames. The compromised agent was connected to downstream agents and services — extending the reach far beyond the initial workstation.

## Impact

The immediate implication is that **every organization using AI coding agents with MCP integrations needs to audit their exposure now.** If your developers use Claude Code or Cursor with the Sentry MCP server, and your Sentry DSN is in public JavaScript (which it almost certainly is, by design), you're in the exposure set.

Beyond Sentry, the "agentjacking" vulnerability class applies to every MCP tool integration that returns externally-influenced data. Security teams that reviewed their MCP server binaries without examining the data sources those servers expose have only addressed half the attack surface.

The governance gap is arguably larger than the technical one. Most organizations have never asked: which data sources are our AI agents permitted to query? Under what conditions? With what content-handling requirements? MCP server authorization needs the same level of review as third-party dependency approval — with explicit scope for what categories of external content are permissible.

Tenet open-sourced [agent-jackstop](https://github.com/tenet-security/agent-jackstop) — drop-in configs that harden Cursor and Claude Code against this attack class by cutting the risk from untrusted telemetry ingestion. Short-term mitigations include: disabling autonomous code execution modes, enforcing human approval before package installation or shell commands from MCP-retrieved content, and running agents in isolated containers with restricted environment variable visibility.

The deeper lesson is architectural: **the implicit trust model between AI agents and MCP-connected services is broken.** Agents treat tool output as ground truth. But tool output can be adversarial. Until MCP evolves content provenance mechanisms — or until agent runtimes learn to treat every byte of external data as potentially hostile — "agentjacking" is just the first of many names for the same attack.

---

*The attack class was documented by Tenet Security Threat Labs (June 2026). CSA's MAESTRO framework and AI Controls Matrix map the governance implications. Sentry's position — "technically not defensible" — makes clear that platform vendors won't close this gap. The only place left to stop it is at the agent's runtime.*
