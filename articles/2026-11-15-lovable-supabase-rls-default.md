# One Missing Policy, 159,041 Apps: AI-Built Sites Were Serving Passports by Default

> A German security firm enumerated a year of Lovable projects and found that a database permission layer — not generated code — was the weakest link in AI-built software.

## What Happened

Crissy Field GmbH, a Hamburg application-security firm, published a measurement study of AI-generated web applications on November 2, 2026, alongside reporting by Handelsblatt. The team used Common Crawl to enumerate **159,041 Lovable projects** created between December 2025 and September 2026. Of those, **34,545 used Supabase** as their database, and **24,664 of them sent database records to the browser on first page load** — no login, no interaction.

That alone is not a bug — plenty of sites publish public data. The researchers then sent additional read-only requests using the same client API key the page had shipped, and looked at what came back. Email addresses appeared in **10,560 tables across 7,403 sites** — 30% of the 24,664 they probed.

One German lifestyle forum served **more than 80,000 user profiles** with full names, email addresses, dates of birth and gender, plus **200,000+ chat messages**. Event organizers and restaurants exposed ticket and food-sales records including phone numbers, postal addresses, payment details and valid ticket codes. A property-management company exposed AI-generated summaries of internal emails about tenants and business partners. Most seriously, one provider exposed **more than 1,000 identity-document records** with passport numbers, expiry dates and nationalities. Other accessible material included tens of thousands of contracts, invoices, personal photo albums, internal WhatsApp conversations, and one set of **280,000 emails** sent to a provider's customers.

The mechanism is mundane. Supabase is designed to be called directly from the browser, which means the client key is public by definition; authorization has to be enforced per row by Row Level Security policies. Tables created through the dashboard inherit safer defaults, but tables created by SQL or an ORM — exactly how a coding agent provisions a schema — can land with no policy at all, and thus no gate. Supabase has since been migrating toward deny-by-default, with the onboarding default changing at the end of October 2026 per the discussion threads linked in the HN thread; projects created earlier keep the configuration they were born with. The researchers withheld their access methods and gave Lovable the affected URLs before publication.

## Why It Matters

Three properties make this different from ordinary appsec attrition.

**The vulnerability is a default, not a bug.** Nothing in the generated code is malformed. The application does what it says. What is missing is a policy that lives in a different system — the database — and that no amount of prompting "make it secure" reliably produces, because the app builder's own mental model ends at the code it can see.

**The failure is invisible to the person holding the risk.** Lovable markets itself as usable without technical knowledge, while its terms place responsibility for applications and data handling on the customer. That gap is the actual product defect: not an insecure framework, but a sales promise that removes the one skill needed to notice the hole.

**Uniformity converts many small leaks into one systemic exposure.** Because the database integration follows recurring patterns, a probe that works against one app works against thousands. The researchers note this explicitly: repeated misconfiguration makes the whole population cheap to scan. Vibe-coded applications are therefore not a long tail of independent mistakes but a monoculture, where a single default decides the exposure of an entire cohort.

The model wrote working software. It did not, and could not, write the authorization layer that the architecture pushed out of its reach.

## Impact

For AI app platforms, generating a schema is not the same as generating a security posture. The durable fix is structural: deny by default at the data layer, refuse to ship privileged keys into the client, generate policies alongside tables, and run differential auth tests in CI — anonymous request versus authenticated request, per tenant. Auto-remediation helpers are a comfort, not a control.

For security teams, the study hands over a cheap audit primitive: enumerate the population, then check what an unauthenticated client key can read. Expect this to become a standard check for any templated backend, the way exposed S3 buckets did.

For organizations, the uncomfortable implication is inventory. Internal tools and "temporary" dashboards built in a weekend now hold customer and identity data, and nobody owns them. Verify per-table policies, rotate keys, remove service-role credentials from anything shipped to a browser, and re-review after every substantial change — automated scanners are not a substitute for someone reading the permission model.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Crissy Field Research](https://www.crissyfield.de/research/lovable-en/) | HN Discussion: [638 points, 271 comments](https://news.ycombinator.com/item?id=50499017)*
