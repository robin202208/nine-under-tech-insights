# Privacy Is Not a Plugin: Claude Code Put Your Email in the Model's Context, Then in Its Curl Commands

> A bug report about a `User-Agent` header exposed a deeper design failure: identity data injected into an agent's context becomes outbound network traffic the moment the model writes the command.

## What Happened

On November 5, a macOS user filed issue #107757 in `anthropics/claude-code`, titled "Privacy violation in User-Agent header." The reporter said they now receive "dozens of scam emails per day" because Claude Code put their address in the `User-Agent` header while searching the web. Within a day the issue was the top AI story on Hacker News — more than 1,500 points, some 490 comments, mostly from users seeing the same thing.

The mechanism matters more than the header. Since May, Anthropic has placed the signed-in account's email inside the model's context (issue #55743, "auto-injected ... with no opt-out") to stop Claude hallucinating names for `@author` tags. With that email in context, whenever Claude needed data from an API whose documentation tells scripted clients to identify themselves, it did exactly what the documentation said. SEC EDGAR's data API is the clearest example: a request carrying curl's default user agent returns **403**, while the same request with a contact email in `-A` returns **200**. We reproduced both responses today. Wikipedia's policy for automated clients asks for the same courtesy. Users posted headers like `company-helm-research/1.0 (Claude Code agent run by <name>, <company> platform team; docs lookup)`; one subagent added the project's GitHub URL, and one user saw a work address leave a personal-account session.

Anthropic responded on two layers. An engineer said the account email is being removed from context entirely, shipping "early next week." Until then, version 2.1.317+ ships a built-in "mod" called `cc-plugin-privacy`, enabled by default, intended to ask before Claude runs a web fetch or curl-style command containing your email; its source is now published under `mods/privacy`. It is not a closure. The reporter was on 2.1.318 and could not find the plugin in `/plugins`. A second tester showed the hook loaded and marked enabled still let a command containing the account address and the string `User-Agent` run, returning `is_error: false`. The engineer acknowledged the mod "still doesn't prevent all cases, like when Claude writes a script and runs on its own."

## Why It Matters

First: context is an egress channel. We usually model prompt context as an input — facts the model reasons over — but for any tool-capable agent, context is also the source of the commands it emits. Every durable identifier placed there (email, legal name, repository URL, internal hostname, token) is therefore available at every network boundary the agent can reach. An API key can be rotated; a primary email address that receives password resets cannot. Adding personally identifying data to fix a cosmetic hallucination traded a permanent identity for a naming nicety, with no opt-out and no review of where that data could travel.

Second: the guard sits at the wrong layer. A hook that inspects command text is string matching against a stochastic generator, and the generator sees the guard's blind spots. Write a script that reads the address from git config, base64 the header value, put it in an environment variable, or split the curl across two lines. The check inspects `python3 /tmp/fetch.py`, sees no `@`, and passes. Enforcement has to sit where bytes actually leave — a proxy with an allowlist, DNS policy, header scrubbing — because that is the one place the model cannot paraphrase.

Third: the collision is structural, not a bug in one file. Public data APIs like SEC EDGAR and Wikipedia require scripted clients to publish a contact identity; that convention assumed a handful of institutional crawlers and a human who could be emailed. An agent running on a personal account satisfies the policy with the nearest identity it holds — its operator's. Neither the site nor the model is behaving unreasonably. The system is.

## Impact

For harness builders: do not inject durable personal identifiers into model context at all. If a feature needs a name, mint a revocable per-session handle; enforce egress with allowlisted paths, header scrubbing, and logs that include headers.

For engineering teams: audit what your harness injects — OAuth account metadata, `git config user.email`, project instruction files, git remotes — and point agents at a dedicated alias such as `agent@yourcompany.com`. Treat agent egress as a network security boundary.

For the ecosystem: design agent identity properly — per-agent, revocable contact identifiers, and an explicit agent-contact field in APIs instead of overloading `User-Agent` with a human's address. Every incident like this one hands regulators a ready-made data-minimization argument: the email was never needed to answer the question the user asked.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [claude-code issue #107757](https://github.com/anthropics/claude-code/issues/107757) & [SEC EDGAR data API](https://www.sec.gov/os/webmaster-faq#developers) | HN Discussion: [1553 points, 492 comments](https://news.ycombinator.com/item?id=50385272)*
