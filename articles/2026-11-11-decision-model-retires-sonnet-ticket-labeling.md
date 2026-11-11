# 44x Cheaper, Same Answer: Amazon Turned Ticket Labeling Back Into a Classification Problem

> A 4B-parameter decision model now labels one of the world's largest internal ticketing systems for about $500 per million tickets — where Claude Sonnet 4.6 cost $22,200.

## What Happened

On November 9, AWS engineers Carr Onstott and Mehul Jaiswal published a migration report from Strands Agents: they removed Claude Sonnet 4.6 from the labeling path of Amazon's internal ticketing system and replaced it with Strands Decider, a model that cannot generate text at all.

Every ticket — customer escalation, capacity request, automated alarm, security finding — had to be sorted into one of 20 categories. The old agent carried a long system prompt on every message, reasoned against a fixed label catalog, and emitted JSON. Sonnet 4.6 had been the "goldilocks" choice for nine months: smart enough, cheap enough, at extreme volume.

A decision model works differently: give it a state and a question with a set of options, and it returns a probability per option plus a confidence score. The state is the ticket. The options are the label catalog. The output is one token's worth of commitment instead of a JSON document scaffolded by reasoning.

They started with the 2B build, found the E4B Gemma 4 version clearly better on held-out tickets, and tuned the confidence threshold that gates whether a label is applied. The serving stack is deliberately boring: SQS, Lambda, and a SageMaker endpoint on `ml.g5.xlarge` (one A10G, 24 GB) holding a 19 GiB model in an isolated container.

The measured deltas: average call time fell from 4,215 ms to 897 ms (78%), total tokens per ticket from 6,724 to 2,010 (70%, with output tokens going to zero), and Lambda execution duration dropped 74%. Cost went from $0.0222 to roughly $0.0005 per ticket — $22,200 down to about $500 per million, a 44x reduction at published list prices.

Two details carry the weight. Most of the token savings were not the ticket: production's label descriptions alone exceeded Decider's 4,096-token window, so the team rewrote all 20 labels as one- or two-sentence criteria, sent as a single multiple-choice question plus a "none" option. And JSON key order changed the answer: reordering identical content moved output probabilities by up to 0.19, so every request now sends sorted keys.

## Why It Matters

Strip the ticket domain away and the arithmetic is a critique of how agents are priced. Of the 4,714 input tokens that disappeared per call, the ticket text was unchanged — the removed mass was label prose, schema instructions, and JSON formatting that Sonnet re-read on every call, thousands of times an hour. Classification agents pay frontier prices to re-parse their own boilerplate.

A generative model answers a multiple-choice question by writing text and hoping the first option parses. A decision model returns a distribution, and a distribution is an operational object: you can threshold it, abstain from it, and route the low-confidence residue to a human or a bigger model. Confidence stops being a prompt trick and becomes a number you can set.

The economics shift shape, not just size. At $3.00 per million input tokens the marginal cost of one more ticket is roughly constant; at $1.408 per instance-hour it is zero, and utilization becomes the only lever. An instance handling 640 ms requests serves about 5,600 tickets an hour: $250 per million at full load, though the team budgets $500 for burst headroom. That is capacity planning, not API billing, and any team can apply it to its own queue depth.

The method matters as much as the model. A three-way AppConfig setting — BEDROCK, DUAL, DECIDER — let both models label the same live traffic and apply Sonnet's answer while comparing, so every disagreement was judged on production data; rolling back was a 45-second config change.

## Impact

The report is explicit about its boundaries. This worked because the task was a closed choice from a known catalog, the volume was high, and the old path's latency mattered. It stops working when labels need explanation text, when the catalog churns weekly, or when the useful context exceeds a few thousand tokens — exactly the cases where the generative model still earns its price.

Anyone running an agent that mostly sorts things should split the pipeline: a decider for the common 20-way call, an LLM for the tail that needs justification. The sizing formula is one division — tickets per hour divided by 5,600 — and the rollout pattern is copyable with a config flag.

The larger signal: "agentic" is stratifying into layers with different cost physics — deterministic rules, small decision heads, mid-size models, and frontier models for open-ended reasoning. Amazon's ticket router did not get smarter. It got honest about what the task always was — a classification problem wearing a chat interface.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Strands Agents Blog (AWS)](https://strandsagents.com/blog/amazon-saves-millions-replacing-sonnet-with-strands-decider/) | HN Discussion: [687 points, 242 comments](https://news.ycombinator.com/item?id=50436640)*
