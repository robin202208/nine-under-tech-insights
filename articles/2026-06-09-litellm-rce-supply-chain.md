# CVSS 10.0: Hackers Are Exploiting LiteLLM to Seize AI Infrastructure With Zero Credentials

> A chained vulnerability in LiteLLM — the most popular open-source LLM gateway — lets attackers execute arbitrary commands on AI infrastructure without authentication. It's being actively exploited in the wild. If you self-host LiteLLM and haven't patched since June 9, assume compromise.

## What Happened

On June 9, 2026, cybersecurity researchers at Horizon3.ai confirmed that threat actors are actively exploiting a critical vulnerability chain in LiteLLM, the open-source proxy that routes API calls to LLM providers like OpenAI, Anthropic, and Azure. The combined attack achieves CVSS 10.0 — the highest severity score possible — and requires zero credentials.

The attack chains two vulnerabilities:

**CVE-2026-42271** is a command injection flaw in LiteLLM's Model Context Protocol (MCP) server test endpoints. The endpoints `POST /mcp-rest/test/connection` and `POST /mcp-rest/test/tools/list` accept arbitrary server configurations — including full commands, arguments, and environment variables — and spawn them as subprocesses on the host. When initially disclosed on April 20, the vulnerability was downgraded because exploitation required a valid proxy API key.

**CVE-2026-48710** dismantled that assumption. It's a Starlette "BadHost" Host header validation bypass affecting Starlette ≤ 1.0.0. By crafting a malicious HTTP Host header, attackers bypass LiteLLM's API key authentication entirely.

Chained together: an unauthenticated attacker sends a crafted request to the MCP test endpoint, bypasses auth via the Starlette header bug, and the LiteLLM process spawns the attacker's command as a subprocess. The command executes with the same privileges as the LiteLLM proxy. No login. No API key. No warning.

Affected versions: LiteLLM 1.74.2 through 1.83.6, on deployments whose dependency tree includes Starlette ≤ 1.0.0. The fix is a coordinated upgrade to LiteLLM 1.83.7+ and Starlette 1.0.1+.

## The Attack Surface

Once code execution is achieved, attackers gain:

- Execution of arbitrary OS commands on the LiteLLM host
- Theft of API keys and model provider credentials stored by the proxy
- Access to all secrets and environment variables in the proxy process
- Lateral movement into connected AI infrastructure and downstream systems

Because LiteLLM sits at the gateway between applications and model providers, it holds credentials for every LLM service an organization uses. A single compromised proxy can expose OpenAI, Anthropic, Azure, and other provider keys — and the attacker can pivot from the gateway to the applications consuming those models.

## Why It Matters

This isn't just another RCE. It's a signal that the AI supply chain has the same attack surface as the traditional software supply chain — and we've been treating it as if it didn't.

LiteLLM has over 20,000 GitHub stars and is deployed in production across thousands of organizations. It's the de facto standard for managing multi-provider LLM access: load balancing, fallback, cost tracking, rate limiting. When you install LiteLLM, you hand it keys to your entire AI stack. The implicit assumption has been that these gateway layers are thin proxies with minimal attack surface. That assumption is wrong.

The MCP test endpoint is the specific failure point worth studying. LiteLLM added MCP server management capabilities — a reasonable feature for an LLM gateway — but the test endpoints accepted arbitrary subprocess spawning as a "test connection" mechanism. This is the classic security mistake of treating an internal debugging feature as if it would never face untrusted input. When the Starlette auth bypass was discovered, that debugging feature became a remote shell.

Horizon3.ai confirmed active in-the-wild exploitation, which means attackers have already mapped the attack surface and weaponized it. Organizations running self-hosted LiteLLM should treat this as an emergency: patch immediately, rotate all stored credentials, and audit logs for anomalous access to `/mcp-rest/test/` endpoints.

## The Bigger Picture

The LiteLLM incident arrives alongside a broader wave of AI supply chain attacks. On the same day, researchers reported that 23 PyPI packages were compromised in a coordinated "Shai-Hulud" attack targeting MCP developers. Threat actors are also abusing ChatGPT, Claude, and DeepSeek brands as phishing lures to steal credentials.

The pattern is clear: as AI infrastructure matures from research projects into production systems, it inherits all the security problems the software industry spent decades learning to manage. Gateways, package managers, authentication middleware — every layer that makes AI easier to deploy also makes it easier to attack. The LiteLLM vulnerability is CVSS 10.0 not because the bug was unusually clever, but because an entire class of AI infrastructure was deployed without the security scrutiny that traditional infrastructure receives by default.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
