# The Framework Tax: 55 AI-Built Apps Show Stack Choice Is Now an Inference Bill

> A hand-rolled benchmark pitted twelve JavaScript stacks against one spec — and found a 1.9× spread in what it costs an agent to build the identical app.

## What Happened

On October 28, Nolan Lawson published a benchmark that treats front-end framework choice as a cost question instead of a taste question. The design: one spec — a small but non-trivial PokéAPI browser with lists, search, and filtering across Pokémon types, moves, and game data — built by Claude Opus 5.5 at high effort inside a sandbox for each of twelve stacks: Hono+htmx, Astro, Nuxt, Vanilla Vite, Next.js, Qwik, TanStack Start, SolidStart, React Router, SvelteKit, Gracile+Lit, and Angular. Each stack got five runs; medians are reported. The agent received the spec-conformance suite and was told to use the latest framework version, but was not told the result would later be scored by Lighthouse, so performance was not optimized on purpose. The harness also forbade the obvious cheat — pre-generating every Pokémon page as static HTML.

The spread is wider than the vibes debate implies. Cheapest to build: Hono+htmx at $2.67 per run, quoted at Claude Code API prices. Most expensive: Angular at $4.97. Wall-clock time tracked cost closely — 8.2 minutes for Astro against 17.2 for Angular — and tokens ran from 3.85M (Nuxt) to 9.76M (Angular). Non-blank source lines ranged from 1,370 (Hono+htmx) to 2,009 (Angular): roughly 70% of the code for the same feature set. Shipping weight diverged far more. Astro emitted essentially zero client JavaScript; Next.js shipped 142 KiB, TanStack Start 118 KiB, React Router 115 KiB. Total Blocking Time followed the bundles: about 0 ms for Astro, Hono+htmx and SvelteKit, 103 ms for Angular. Largest Contentful Paint ran from 810 ms (Astro) to 2,090 ms (Next.js).

Lawson is candid about the noise. Variance is wide enough that the cheapest React Router run ($2.82) undercuts the priciest Hono+htmx run ($3.19). One Next.js app lazy-loaded its images and four did not, which explains much of the LCP swing. Two Angular runs produced SSR servers that rejected non-localhost requests and were counted as failures rather than re-rolled.

## Why It Matters

Three mechanisms sit under these numbers. First, training-data recency is a visible cost line. SvelteKit v3 is new enough that agents opened the migration guide on every single run; for Gracile, agents cloned the repository or read compiled sources out of node_modules just to learn the API. A framework the model has never seen is not necessarily worse — it is simply billed by the hour while the agent teaches itself.

Second, the distribution layer for agent tooling is immature. Not every stack shipped an MCP server or skill, and where one existed the agent often ignored it: Nuxt's MCP was never used, Next.js's skill file was read in one run out of five, and TanStack Start's skill files — buried in node_modules — were found twice in five. Tooling that is present but undiscoverable is indistinguishable from no tooling. Lawson's suspicion that the current crop of MCPs and skills may be doing more harm than good is, at minimum, not refuted by his data.

Third, and most structurally, the unit economics of front-end work changed. Code size is no longer only a maintenance tax paid by humans; it is context that the model re-reads on every subsequent edit, so a framework that inflates a codebase by 45% inflates every future turn. The counter-intuitive result cuts the other way too: "Vanilla" with Vite, given full latitude, cost $2.98 and took longer to build than five frameworks. Some structure beats none, because the agent inherits scaffolding and conventions instead of inventing them.

## Impact

For engineering leaders, framework selection has quietly become a recurring inference cost rather than a one-time learning cost. Stacks an agent reads fluently — recent, well documented, conventionally structured — finish cheaper and produce smaller diffs, which compounds across a project's life.

For framework authors, the benchmark marks a promotion path: shipping an accurate, discoverable skill file and keeping documentation inside training-data reach is now a product feature, not marketing.

The most reusable artifact, though, is the harness itself. Its conformance suite reads the accessibility tree rather than the raw DOM, so it can verify behaviour without dictating markup, and it explicitly blocks the degenerate static-site shortcut. Lawson put it on GitHub and invited forks from other frameworks and agents. The claim stays modest: not "pick this framework," but that evidence-based stack decisions remain possible in an era when nobody is typing the code.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [The AI cost of JavaScript frameworks](https://nolanlawson.com/2026/10/28/the-ai-cost-of-javascript-frameworks/) | HN Discussion: [294 points, 228 comments](https://news.ycombinator.com/item?id=50323088)*
