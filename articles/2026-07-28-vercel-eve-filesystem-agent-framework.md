# Vercel Eve: The Filesystem Is Now the Agent Framework

**2026-07-28** · 9 min read · Agent Engineering, Open Source

---

## What

Vercel released [Eve](https://eve.dev), an open-source TypeScript framework where an AI agent is a directory of files. Not a metaphor. Not a config file that generates files. The directory *is* the agent.

```
agent/
  agent.ts              # the model it runs on
  instructions.md       # who it is
  tools/                # what it can do
    run_sql.ts
  skills/               # what it knows (loaded on demand)
    revenue-definitions.md
  subagents/            # who it delegates to
    investigator/
  channels/             # where it lives
    slack.ts
  schedules/            # when it acts on its own
    monday-summary.ts
```

Add a tool by adding a TypeScript file. Add a skill by adding a Markdown file. Eve discovers them at build time and wires them into a working agent. No registration boilerplate. No hand-rolled loops. The filename and its place in the tree are the definition.

This is not a demo. Vercel runs over 100 Eve agents in production today — a data analyst handling 30,000+ questions a month, an autonomous SDR returning 32× its $5,000/year cost, a support engineer solving 92% of tickets on its own. The framework they open-sourced is the one they actually use.

Under the hood, Eve ships with production infrastructure pre-integrated: durable execution via the Workflow SDK (sessions survive crashes and deploys), sandboxed compute via Vercel Sandbox (agent-generated code never touches your app runtime), human-in-the-loop approvals at the tool level, subagents with per-agent model selection, multi-channel deployment (Slack, Discord, Teams, Telegram, HTTP, GitHub), OpenTelemetry tracing, and a built-in evals framework. All of it compiles to a standard Vercel Functions project. `vercel deploy` ships the same directory that ran on your laptop.

## Why

Every team building production agents has been hand-rolling the same six things: state persistence, sandboxed execution, human approval gates, credential brokering, observability, and multi-surface routing. These aren't differentiators. They're plumbing. And every team reinvents them from scratch because the agent framework landscape has been split between research-first frameworks (LangChain, CrewAI) that prioritize flexibility over production readiness, and platform-locked solutions (OpenAI Agents SDK, Google ADK) that work great on one cloud.

Eve takes the third path: **convention over configuration for agents**. This is the exact strategy that made Next.js the default React framework. Before Next.js, every React app hand-rolled routing, SSR, code splitting, and image optimization. Next.js said: put files here, we handle the rest. Eve says the same about agents.

The implications are deeper than convenience. A filesystem-first agent is:

- **Git-native.** Instructions, tools, and skills are commits. A prompt change has a diff and a review. CI runs evals before deploy.
- **Agent-readable.** Eve bundles its own docs inside the npm package, so your coding agent can read `node_modules/eve/docs` and scaffold, extend, or debug an agent without you.
- **Observable by default.** Every model call and tool invocation produces an OpenTelemetry span. No instrumentation code required.

The fact that Vercel dogfoods this at scale matters. When Guillermo Rauch says agents now trigger 29% of all Vercel platform deployments — up from 3% a year ago — and that they expect half of all deployments to come from agents soon, the framework they use to orchestrate that traffic is worth studying.

## Impact

Eve changes the economics of building production agents in three ways.

**First, it collapses the prototype-to-production gap.** The same directory that runs locally deploys to production unchanged. The sandbox adapter swaps from Docker to Vercel Sandbox without a code change. A session mid-task when you push finishes on the version it started on. This eliminates the rewrite step that kills most agent projects between "works on my machine" and "running in Slack."

**Second, it standardizes agent architecture.** The directory layout is a contract. A new team member looking at `agent/` knows exactly where everything lives. Cross-team agent reuse becomes possible because every agent speaks the same structural language. This is how Vercel consolidated 100+ agents from separate stacks into one monorepo. The shape is the same. The conventions carry over.

**Third, it shifts the bottleneck from infrastructure to iteration.** When shipping an agent requires provisioning a database, configuring a sandbox, setting up tracing, and wiring approval flows, teams ship one agent and stop. When those come for free, the bottleneck moves to what matters: writing better instructions, building sharper tools, and tuning evals. The agent becomes software you improve, not infrastructure you maintain.

For the broader ecosystem, Eve validates that the agent framework is a distinct category warranting its own abstractions — the same way React frameworks split from vanilla React, or web frameworks split from raw HTTP servers. LangChain abstracted the LLM call. Eve abstracts everything around it: durability, isolation, approval, observability, and deployment. The agent harness is no longer the implementation detail. It's the product.

```bash
npx eve@latest init my-agent
```

One command. A running agent. Production included. That's the bar now.
