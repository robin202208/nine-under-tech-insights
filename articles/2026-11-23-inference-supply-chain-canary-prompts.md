# 2,000,000 Canary Prompts: Mapping Where AI Inference Data Really Goes

> Dragos hid fake credentials inside two million prompts and watched. Stolen keys reached the front door of three providers, a US-hosted request resurfaced in a Feishu chat 24 days later, and prompts flagged zero-retention were read anyway.

## What Happened

Over five months, from July through November 2026, Dragos chief research officer HD Moore's team sent roughly two million prompts to widely used AI services. Each carried secrets that could exist in one place only: a unique URL, a unique DNS name, and a live AWS key bound to an empty, fully logged account. Any use proves that some system saw the prompt, and ties the access back to the exact prompt, model, router, and privacy setting.

At least three hosting providers were actively compromised. Intruders copied requests at the provider's front door; some stolen keys came from requests rejected before a model ever ran. Keys were tested within hours, then used to list S3 buckets, read secrets stores, and mint new IAM users. The bait generated tens of thousands of AWS API calls, and one key stayed in use three weeks after seeding. One actor worked two providers from a single rented server, rotating from a scripted browser to Boto3 to the AWS CLI on Kali, then adding ARTEX, an LLM-driven penetration-testing framework whose user agents appear in no public indicator list.

Marketplace resellers harvested prompts without exploiting anything. On an open inference marketplace, a seller lists a popular model at the lowest price, enables request logging, and waits. Two prompts sent through one seller were opened about 36 hours later from home broadband, followed by username guessing at the login page. Another seller's prompt passwords were typed by hand into SSH root attempts, four times in two minutes, from a Mac in France. Prompts for the same model through a different seller were untouched: the leak follows the seller, not the marketplace.

Privacy flags did not bind. A Chinese lab fetched the secret URLs from more than half the prompts sent to its models between July and October — including roughly two-thirds of those marked zero data retention — typically two or three days after inference. US providers reselling that lab's models triggered the same scraper, so customers picking a US host for residency got neither residency nor privacy. Twenty-four days after a ZDR-flagged request to a US provider, the prompt's secret link was posted into a Feishu channel and opened from three desktops and three phones in Beijing and Shanghai within two minutes. Dragos had disclosed only eight characters of the link; whoever shared it held a stored copy.

A traceroute for inference — an image URL reporting IP and user agent, plus malformed requests whose nested error envelopes expose each hop — traced about 2,000 routes, several hundred of them through an unidentified system.

## Why It Matters

Routing is now a security boundary, and the one nobody inspects. Every hop sees the entire prompt and response, and routers routinely forward to other routers. A privacy flag binds the first hop and nothing after it; once an attacker is inside a provider, retention policy stops mattering.

The economics push the wrong way. Inference data carries confidential source code, internal strategy, and live credentials — worth more per byte than the GPU time that produced it. A seller can lose money on each request and still profit from the logs. Buyers see a price and a model name; they never see the path.

Canary tokens turn an assertion into a measurement. Until now, zero retention was a contract clause and an act of faith. One unique URL per prompt makes it telemetry — timestamp, IP address, user agent — attributable to a specific request. Any team can run that, not just a research lab.

## Impact

The response has begun. OpenRouter off-boarded three providers, removed 125 endpoints from zero-retention eligibility, notified customers, and started verifying network paths for unauthorised forwarding. Together AI disclosed prompt samples stolen from a private Hugging Face repository after an AI coding agent committed a credential to a public GitHub repo — then made ZDR the default for new accounts.

For builders: pin upstream providers instead of accepting a default pool; seed a fake credential in production prompts and treat its use as an incident; scan prompts for secrets before they leave your network, because agents send whatever they can read. If the work is sensitive enough, buy isolation — a 512 GB Mac Studio at $22,299, a DGX Spark at $6,950, a DGX Station near $105,000 — a rounding error beside a leaked production key.

Expect the next compliance question to be provenance rather than retention: not "do you store my data" but "prove where each request went."

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Dragos — AI Supply Chain Security: What Canary Tokens Found](https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens) | HN Discussion: [101 points, 22 comments](https://news.ycombinator.com/item?id=50596511)*
