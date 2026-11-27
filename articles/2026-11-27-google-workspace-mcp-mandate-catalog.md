# 23 or 30 Tools: The Catalog Google Now Requires Every Agent to Trust

> On November 24, Google made MCP the only sanctioned door into Workspace. Two days later, its Gmail server gave the same request two different answers about what is behind it.

## What Happened

Three things landed on November 24: eight hosted Workspace MCP servers went generally available (Gmail, Drive, Docs, Sheets, Slides, Calendar, Chat, Tasks) alongside an official `gwscli`; a Workspace AI Tools Addendum joined the Google APIs Terms of Service; and Workspace quotas were re-tiered.

One sentence carries it: a tool that "is or is driven by an AI Agent, an AI Harness or an AI model or service employing an AI model" must go through a Google MCP, and disguising an agent to dodge the classification is separately forbidden. Enforcement starts February 1, 2027: agents that keep calling REST directly are pinned to Standard-tier quotas whatever tier the project pays for. OAuth clients must be flagged "Use this client for an AI-powered agent"; servers have rejected unflagged clients since November 11.

The public Gmail toolset searches and reads threads, creates and reads drafts, manages labels, trashes and spams. Eight tools are partner-only, and they are the interesting ones: `send_message`, `reply`, `forward`, `update_draft`, `create_filter`, `list_filters`, `update_label`, `delete_label`. An agent can draft a reply but cannot revise it. Anthropic's Workspace connector advertises send, reply and forward — that is partner access.

Quotas are the second constraint. Standard tier is 5,000 requests per day per project per product, and `search_threads` and `list_drafts` each count as 10, so a search-only agent gets 500 searches a day across all its users. Scaled-tier billing begins April 1, 2027: MCP is billed from the first request while REST keeps large free daily allowances.

The catalog. Frank Chu probed all eight servers on November 26; `tools/list` answers without a token, so the catalog is public: Drive 9 tools, Docs 2, Sheets 6, Slides 4, Calendar 9, Chat 4, Tasks 9. Gmail returned 23 tools 18 times and 30 tools 12 times across 30 identical calls in 11 seconds — 88,436 bytes versus 149,735 — and the 30-tool variant adds exactly the seven gated write tools. Protocol headers changed nothing. The server reports as `StatelessServer`, version `ESF`; the first call hung until an `initialize` was sent. Whether those tools execute for a non-partner is untested; Google documents say partner access is required.

## Why It Matters

OAuth was a claim about intent: a valid token meant a human had decided. Google's argument — that policy checks, data boundaries and confirmations now have to live in the request path — is correct, and one governed gateway is the right shape for it. The friction is that the gate replaces capability, not merely access. The tool list becomes the ceiling of what a non-partner agent can do, and the vendor decides who gets the ceiling raised. Same scopes, same consent, different tool list: a licensing distinction dressed as a security control. It also writes "is it an AI?" into API terms as the regulated category, making any script with a model behind it a classification problem.

Three details matter. At 10 units per search, metering rather than the user sets how much an assistant may look around. On the Scaled tier the mandated path is billed from request one while REST keeps its free allowance, so compliance costs more than the behavior it replaces. And the retention clause caps "Google Technical Information" — explicitly vector embeddings, memory indexes, user profiles, organizational graphs — at 30 days, ruling out the persistent inbox index most assistant products are built on.

Clause 2(e) forbids scraping or "systematic probing" of the MCP servers beyond what a user-authorized task requires. A nondeterministic catalog plus a ban on systematically checking it makes an awkward pair: the interface you must build on is one you cannot verify.

## Impact

Assume write access is unavailable and design draft-then-human-send; create drafts instead of mutating them. Treat memory as rebuildable within 30 days — store pointers, re-derive embeddings, never let a vector index become durable state, and wire deletion to disconnect events. Handle discovery at runtime: parse the catalog at startup, tolerate both 23 and 30, degrade when a tool is absent, and treat a listed name as no proof of a callable tool.

Budget quotas in product terms. At 10 units per search, 5,000 a day is 500 searches project-wide, so cache hard or buy Scaled tier, where no MCP request is free.

Two dates set the schedule: February 1, 2027 for the MCP requirement, April 1, 2027 for billing. Teams shipping Workspace agents have a quarter to answer a question Google has not: whether a deterministic script with a model behind it is an "AI Harness," and how that answer arrives — as a documented tier, or as a 429.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Sources: [Google Workspace AI Tools Addendum](https://workspace.google.com/terms/ai-tools), [TechStackups — Google Will Make AI Agents Use Its Workspace MCP Servers](https://techstackups.com/articles/google-workspace-ai-agents-mcp), [frank chu — 23 or 30 tools?](https://dev.to/frankchu/23-or-30-tools-googles-gmail-mcp-cant-decide-59j3) | HN Discussion: [238 points, 203 comments](https://news.ycombinator.com/item?id=50653477)*
