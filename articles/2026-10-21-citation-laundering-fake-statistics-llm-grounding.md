# Citation Laundering: How 50,000 Fake Statistics Became Evidence LLMs Cite

> A $50 experiment filled a new domain with invented numbers. Google buried it; ChatGPT kept fetching it — and then a bank and an academic paper cited it.

## What Happened

In March 2026, Metehan Yesilyurt, a generative-engine-optimization (GEO) researcher now at Peec AI, bought a fresh domain called stateglobe.com and generated 50,000 statistics pages in a single day with GPT-4.1. The prompt was little more than a list of 200 countries, a set of topics — SEO, content marketing, social media, e-commerce, web technology — and two sentences of instruction. Later he added 15,000 listicles. Total cost: roughly $50. Every number was invented, and none was checked.

For a week the site indexed almost completely through bulk indexing tools and picked up search traffic. Then two Google updates landed and visibility collapsed; seven months on, the property reports 5 to 20 impressions, effectively deindexed.

ChatGPT never stopped. In the last 30 days, Cloudflare logs show more than 600,000 bot requests to the site, roughly 72,000 of them from ChatGPT-User — the agent that opens a page in real time while answering a question, not a training crawler. Analytics attribute more than 10,000 sessions to ChatGPT since launch. Ahrefs and Semrush count 330+ referring domains, among them Shopify, DHL and Bluehost. The invented figures have been cited in an academic paper, in investment research from Qatar's largest financial group, in an Indian economics publication and in a Canadian community research report. On October 7 the site ranked second for the prompts Yesilyurt tracks, behind only Statista. A follow-up page built on verified, sourced statistics attracted links almost immediately.

## Why It Matters

The mechanism is dull, and therefore durable. When a model writes, it decomposes the task into fanout sub-queries and appends modifiers such as "statistics," "data," "report," or the current year. Retrieval then operates on passages, not pages, so a single self-contained number survives the cut where a nuanced paragraph does not. A reranker scores the passage for relevance to the query and for surface authority; it does not score it for truth. Checking one statistic means locating independent sources, reconciling conflicts, and deciding which number wins — work that no latency or cost budget spanning billions of prompts can absorb.

A number is also the densest retrievable unit of text: specific, quotable, additive. Put the year in the title, the headings and the URL, serve plain server-rendered HTML, and the page signals exactly what grounding pipelines reward.

What follows is laundering. A fabricated figure starts with zero authority. An academic paper cites it. A bank's research cites it. The next reader — or the next model — sees a number attached to a citation, and citation reads as verification. Nobody in that chain lied twice; each link simply treated the previous one as evidence. The terminal risk is the corpus itself: crawled pages become pretraining data, and a model's guess becomes background knowledge that later models reproduce with no source attached at all.

The Google half of this story is oddly reassuring — conventional spam defenses worked once the ranking surface was contested. The retrieval half is not, because the party with the strongest incentive to contest it is not the party being graded on it.

## Impact

For anyone shipping retrieval-augmented generation or agent systems, the operational lesson is that "cited" is not "verified." Numeric claims deserve their own validation pass: cross-source agreement, publication date, and whether the cited page itself cites anything. Provenance belongs in retrieval metadata, not only in the prompt.

For evaluation teams, this is a cheap adversarial test — ask a grounded model for a statistic and check whether it distinguishes a sourced figure from a merely confident one. Most current benchmarks cannot.

For publishers, the constructive version works too: one claim per sentence, source and year beside every number, headings that match model queries, server-rendered HTML, and a check that robots.txt or a CDN rule is not silently blocking ChatGPT-User, OAI-SearchBot or PerplexityBot. Monitoring now means reading AI retrieval bots in your logs, not just Googlebot.

For dataset curators, synthetic-statistics domains are contamination sources, and lineage tracking before pretraining is far cheaper than decontamination afterwards.

Caveats: the author sells GEO tooling, the figures are self-reported, and one operator's experiment is not a prevalence estimate. The mechanism, though, does not depend on the sample size.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Peec AI — Publish stats, get cited](https://peec.ai/blog/publish-stats-get-cited-the-tactic-that-put-my-data-all-over-the-web) | HN Discussion: [323 points, 186 comments](https://news.ycombinator.com/item?id=50165801)*
