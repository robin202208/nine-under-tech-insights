# Your Agent, Your Cloud: Talorys Puts a Personal AI Assistant Inside Your Own Cloudflare Account

> A single command deploys a private, single-user AI agent — chat, memory, tasks and scheduled reminders — onto Cloudflare's free tier, with no vendor server in the loop.

## What Happened

On October 10, developer "rociiu" released **Talorys**, an open-source (MIT, TypeScript) personal AI assistant that runs *entirely inside your own Cloudflare account*. Installation is one command — `npx create-talorys@latest` — and the repo pulled roughly 394 stars within a day, reaching 249 points and 122 comments on Hacker News.

The architecture is three layers. The browser loads a static React/Vite app from a `*.pages.dev` site; requests to `/api/*` hit a Cloudflare Pages Function that forwards the **original request** over a *service binding* to a private Worker (a Hono router). That Worker forwards to `getAgentByName(env.TalorysAgent, "personal-agent")`, a Durable Object built on the Cloudflare Agents SDK. The Durable Object holds everything — conversations, memories, tasks, notes, projects, automations, sessions, usage — in a single SQLite store, and uses Durable Object alarms as the scheduler. Chat runs on Workers AI (`@cf/zai-org/glm-4.7-flash`) with streaming and tool calling.

The Worker is deployed with `workers_dev: false` and `preview_urls: false`, so it has **no public URL** and is reachable only through the Pages service binding; the agent instance name is fixed server-side, so a client cannot address another agent. To fit the free tier, Talorys deliberately provisions no KV, D1, R2, Vectorize or Workflows — one Durable Object holds all data, static assets never invoke a Function, and reminders and digests are deterministic rather than AI-driven.

The security model treats the `pages.dev` endpoint as internet-exposed. The owner password is hashed **on the installer's machine** (PBKDF2-HMAC-SHA256, 100,000 iterations, 16-byte salt) and stored as a Worker secret. Sessions are 256-bit tokens in `HttpOnly; Secure; SameSite=Strict` cookies; the database stores only `HMAC(SESSION_SECRET, token)`, so a storage leak cannot be replayed. Destructive tools require an explicit confirmation in the owner's own message, enforced in code rather than in the prompt, and scheduled AI runs receive read-only tools. None of the agent's tools have shell, code execution, outbound HTTP or credentials.

## Why It Matters

This inverts the dominant deployment model for personal assistants. Most "AI assistant" products are multi-tenant SaaS: your conversations and memories sit in a vendor's database, subject to its pricing and privacy policy. Talorys keeps the vendor out of the loop entirely — no server or account operated by its developers, no telemetry. Your data lives in your account, and your Cloudflare bill is the only recurring cost.

The more interesting lesson is how much a *single* serverless primitive can carry. A personal agent needs two things: durable, queryable state, and a way to wake up on a schedule ("remind me tomorrow at 9"). Durable Objects supply both — SQLite for the store and alarms for the scheduler — which is why Talorys can refuse the usual sprawl of KV, D1, R2 and Vectorize and still fit comfortably on the free plan.

It also sketches the security posture any self-hosted agent will need. Because the front end is public, safety rests on a private backend with no routable URL, strict session hygiene, CSRF defense via a required custom header plus `SameSite=Strict`, and destructive actions gated in code. Marking notes, memories and tool output as untrusted *data* addresses prompt injection directly. And because the model sits behind an `AIProvider` interface, the lock-in is the runtime, not the model — swap Workers AI for OpenRouter or Gemini by implementing one class.

## Impact

For individual developers, this lowers the floor on owning an agent. You can stand up a private assistant in one command using primitives you may already pay for, and the installer is written to reconcile — re-running it resumes rather than duplicating resources, migrations are append-only, and a reinstall never deletes data.

The reusable patterns are the real deliverable: a Worker kept private behind a service binding; a single Durable Object acting as both database and cron; AI calls that degrade gracefully when the daily Neuron allocation is exhausted (tasks, notes and reminders keep working with no model at all); and per-tool permission tiers with code-enforced confirmation.

The trade-offs are honest and worth weighing. Talorys is single-user by design — no signup, no teams, no sharing. Memory retrieval is FTS5 keyword search rather than embeddings, which caps how fuzzy recall can be. Reasoning is disabled to conserve Neurons, and free-tier quotas are account-level and set by Cloudflare, so "free" is bounded rather than unlimited.

The larger signal is about where an agent's memory should live. As assistants accumulate personal context, that state — not the model — becomes the sticky asset. Talorys is a working argument that "your agent, your cloud" can be both cheap and private, and that the infrastructure for personal AI is converging on serverless primitives an individual developer can actually own.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [GitHub — rociiu/talorys](https://github.com/rociiu/talorys) | HN Discussion: [249 points, 122 comments](https://news.ycombinator.com/item?id=50031614)*
