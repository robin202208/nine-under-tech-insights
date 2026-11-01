# One Click on a Shared Chat: How a Math Keyboard Turned a ChatGPT Link into Account Takeover

> A public conversation share, an unauthenticated `postMessage` listener, and a YouTube allowlist — three harmless decisions that chained into JavaScript running as `chatgpt.com` inside a victim's signed-in session.

## What Happened

On October 18, 2026, researchers at Parameter Labs reported a chain that let an attacker take over any ChatGPT account with a single link. The victim did not install an app, join a project, paste code, or open developer tools. Opening a shared conversation was enough. OpenAI reproduced it, escalated severity from P2 to P1, fixed both versions of its web frontend on October 26, and paid a $5,000 bounty.

The entry point is ChatGPT's Preview feature, which renders an HTML code block as a small working page inside a restricted frame on a different origin. That frame genuinely could not read `parent.document` — the sandbox worked. But the conversation also contained an ordinary interactive graph, and the graph's math library, MathLive, registered a `message` listener in the *main* ChatGPT page to control its on-screen keyboard.

The handler checked the message type and called an origin validator, but not the `event.source` of the sending window, and it accepted `update-setting` messages whose keyboard `layouts[].layers[].markup` could contain raw HTML. So the sandboxed Preview simply posted a message through the mail slot: first replacing the keyboard layout with attacker HTML, then asking MathLive to show it. Rendering performed the equivalent of `container.innerHTML = MathfieldElement.createHTML(keyboardHTML)`, where `createHTML` was an *identity* Trusted Types policy — a wrapper, not a sanitizer.

A script injected that way would be inert, but an unsandboxed `srcdoc` iframe forces a fresh HTML parse and inherits the embedding `chatgpt.com` origin. ChatGPT's CSP then blocked inline code but allowed `www.youtube.com` under `script-src-elem`. The YouTube oEmbed endpoint returned HTTP 200 with `Content-Type: text/javascript; charset=UTF-8`, `X-Content-Type-Options: nosniff`, and no `Cross-Origin-Resource-Policy` header, so a JSONP-style callback became the executor. Because Google rewrote angle brackets, the payload used classic `function` syntax and ended with `;void` to consume the error object the endpoint appends. The chain survived a hard refresh.

From there the same-origin frame parsed `#client-bootstrap` out of the parent document, extracted the session access token and account ID, and called `GET /backend-api/conversations` and the conversation detail endpoint with `Authorization: Bearer` and `ChatGPT-Account-ID` headers — falling back to the legacy `/backend-api/conversation/<id>` route on 403, 404, or 405 — then shipped a selected value to an external server.

## Why It Matters

Every individual design decision here was defensible. Preview was isolated. Graphs may load a keyboard. Frames may exchange messages. CSP may allow a major media provider. Public conversations may be shared. The vulnerability existed only in the seams.

The deepest lesson is that `postMessage` is an authentication boundary, and most implementations treat it as a formatting convention. Checking `event.origin` alone is insufficient: you must also verify `event.source` is the exact window you granted a capability to, and you must never accept state-changing payloads that carry markup. Similarly, Trusted Types policies are often deployed as an "identity" shim to silence browser warnings — that grants zero protection while creating the impression of a sanitizer.

CSP deserves a harder look too. An allowlist that includes any third-party endpoint returning `text/javascript` is a JavaScript delivery channel, not a defense. Here CSP was the last barrier and it failed.

What makes this specifically an AI-product problem is the blast radius behind the token. A cross-site script in a shopping site steals a session; the same script in a chat product inherits months of private prompts, uploaded files, drafts, and business context, plus any connector or tool the account has authorized. And AI products ship exactly the feature set that produced this bug: user-generated artifacts, previews, public share links, embeds, and a frontend dense with third-party rendering libraries.

## Impact

For anyone building an agent or chat frontend: enumerate every `addEventListener("message")`, validate both `origin` and `source`, keep raw HTML out of cross-window settings, sandbox every dynamically created document including `srcdoc`, replace identity Trusted Types policies with real sanitizers, and treat public share routes as attacker-controlled input that reaches the signed-in session.

Architecturally, stop putting bearer tokens and account IDs in same-origin-readable bootstrap blobs: one XSS then equals complete API access. Short-lived, audience-scoped tokens, or moving privileged calls behind a separate origin with its own credentials, shrink the payoff of the next rendering bug.

Expect this pattern to be cloned: artifact previews, shared notebooks, and embeddable canvases exist across every major assistant. A $5,000 bounty for full account takeover also underlines how badly AI products underprice this class of report — and why enterprises should treat assistant web apps as identity-bearing systems, not chat windows.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Parameter Labs — Do the Math: How We Took Over ChatGPT Accounts](https://www.parameter.ai/labs/we-found-a-gap-at-openai) | HN Discussion: [302 points, 149 comments](https://news.ycombinator.com/item?id=50314699)*
