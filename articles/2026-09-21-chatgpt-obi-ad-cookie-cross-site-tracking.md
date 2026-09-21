# ChatGPT's Ad Pixel Knows What You Did on 936 Other Websites

> A one-year, `SameSite=None` cookie on `.openai.com` resolves ChatGPT accounts — and logged-out devices — to advertiser pixels, and the SDK that plants it scrapes more identity from a page than the advertiser ever supplies.

## What Happened

On 2026-09-20, Jamie Larson (Buchodi's Threat Intel) published a reproduction of OpenAI's off-site ad measurement chain: `bzr.openai.com` — `bzr` for *bazaar*, OpenAI's internal name for its ads platform — sets a cookie called `__obi` on `.openai.com`, and that identifier is returned to OpenAI from ordinary websites. He verified it on his phone with two independent capture methods, against months of traffic covering 936 advertiser pixels across 1,029 hostnames.

The handshake is account-bound. On `chatgpt.com` the client generates 16 random bytes and calls `POST /backend-api/bazaar/obi/sync-token`. The backend returns an RS256 JWT naming the issuer `chatgpt-wadi`, the audience `bzr.openai.com`, the purpose `obi_sync`, `sub` set to the account subject, and an `obi` identifier — expiring in 60 seconds. The client posts that token cross-site to `bzr.openai.com/v1/obi/sync`, which answers with `Set-Cookie: __obi=…; Domain=.openai.com; HttpOnly; Max-Age=31536000; SameSite=none; Secure` — the attributes a cookie needs to travel cross-site.

Any company buying ads on ChatGPT installs OpenAI's measurement pixel on its own site, the way retailers already install Meta and Google tags. That load alone discloses the identifier: on a phone carrying `__obi`, three request classes sent it — the `<script src>` fetch of `bzrcdn.openai.com/sdk/oaiq.min.js`, the conversion `POST /v1/sdk/events`, and the SDK's "no credentials" path, which does not help, since the browser attaches cookies to the script request before any OpenAI code runs.

The SDK labels four identity sources itself: `in` for values the advertiser passes deliberately, and `fm`, `ht`, `js` for values it scrapes from form fields, rendered page text, and the tag-manager bus. Scraped identity outnumbered advertiser-supplied identity 685 events to 255. The tag-manager bus is the largest email source: the SDK overrides `window.dataLayer.push` and hunts renamed GTM layers via the `l=` parameter on `gtm.js`. Email, phone and name are SHA-256 hashed before transmission; country, region, city and postal code go in the clear — postal code was the most-harvested form field, 100 events across 28 sites. URLs are cut to origin plus path, but paths reaching the collector included a medical condition, a debt-solutions funnel and a litigation intake form. Automatic matching was on for 638 of 881 pixels with a known setting — every credit and lending advertiser observed.

## Why It Matters

Structurally this is ordinary adtech — a logged-in account, third-party cookies on pixel fires, off-site conversions resolved to a profile. Meta built the equivalent years ago. What has no precedent is running it on an AI chat product, where people volunteer what they would not post on a social network and which increasingly acts on their behalf.

OpenAI's cookie policy lists `__obi` under *Analytics*, one-year, and it is the only entry in that section. Consent is split into `oai_consent_analytics` and `oai_consent_marketing`, yet every sync token decoded carried `consent_decision: analytics_allowed` — so a user who allowed analytics and refused marketing still receives a cross-site identifier. Nor is it gated on sign-in: of 932 decoded tokens, 736 were `account_user` and 196 were `anonymous`, the anonymous subject as stable as the account one, persisting at least 27 days.

Two details close off the easy objections. First, `__obi` is the only OpenAI identifier configured `SameSite=None` — `oai-did`, `oaicom-stable-id`, `oai-client-auth-info` and session cookies were all blocked on the same advertiser-page requests. Second, advertisers cannot see any of it: `__obi` lives on a domain their scripts cannot read, so a site owner who installed the pixel has no way to know their visitors are being resolved to a ChatGPT identity, or that their own page text is feeding `ht` events.

## Impact

The practical takeaway: an "analytics" consent label does not describe what this cookie does, so compliance reviews should read cookie attributes, not category names. Sites running the OpenAI pixel should audit what their rendered text and form fields expose, and check the automatic-matching toggle in OpenAI's Ads Manager — its denylist (passwords, one-time codes, card numbers, SSN, medical history) is a blocklist, not a design constraint.

For builders, the lesson is architectural: identity for attribution, not for the product, is attached at the browser layer before any opt-out logic you write can run — a single `<script>` tag is enough. It cannot operate on iOS (Safari's Intelligent Tracking Prevention blocks third-party cookies and Chrome on iOS runs on WebKit); on Chrome for Android roughly one in five ChatGPT sessions yielded a sync token, and Larson notes the server-side join itself was not directly observed. OpenAI Support acknowledged his 14 September inquiry without answering either question.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Buchodi's Threat Intel](https://www.buchodi.com/chatgpt-now-knows-what-you-do-on-other-websites-via-ad-collector/) | HN Discussion: [573 points, 308 comments](https://news.ycombinator.com/item?id=49776729)*
