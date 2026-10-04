# Germany's Kolibri Is a Sovereign LLM Whose Hardest Problem Was German

> Aleph Alpha's 78B open-weight model is a lesson in what "sovereign AI" actually requires: a tokenizer, a data pipeline, and a model trained to say "I don't know."

## What Happened

On 3 October 2026 — German Reunification Day — Aleph Alpha released Kolibri, a German-and-English open-weight model under the Apache 2.0 license. It is a mixture-of-experts transformer with 78.1 billion total parameters, of which only 3.46 billion are active per token. It was trained from scratch on 768 NVIDIA B200 GPUs running on infrastructure in Germany and Finland, consuming roughly 20 trillion pre-training tokens (about 24 trillion including mid-training and long-context adaptation), with German making up 21.3% of the pre-training mix. Native context is 262,144 tokens, validated up to 1,048,576. The weights are on Hugging Face under Apache 2.0; the training code stays proprietary.

Kolibri is the second model out of what the company calls its Model Factory. A 30B "Kolibri Origin" finished pre-training on 11 June; Kolibri finished on 11 September with 2.5x the parameters, 16x the context window, and nearly 3x the tokens. The pipeline is versioned as GitHub Actions workflows, checkpoints land roughly hourly and are automatically evaluated, and over 21 days of pre-training the run absorbed 38 unplanned interruptions — about one per 10,000 GPU-hours — restarting automatically from a checkpoint.

## Why It Matters

The sovereignty framing — build it in Europe, run it on your own servers, inherit compliance — is the politics. The engineering is more interesting, because nearly every hard decision assumes German is not English.

**The tokenizer.** English-trained tokenizers shred German compounds: the Federal Constitutional Court (`Bundesverfassungsgericht`) becomes `Bund|es|ver|fass|ungs|gericht` under GPT-5's `o200k_base`. Aleph Alpha trained a 128k bilingual vocabulary with UniBPE, which keeps byte-pair encoding's bottom-up merging but selects each merge with the Unigram objective. On German web text it packs 4.90 bytes per token, the best in their comparison (GPT-5: 4.35). A community replication on the German constitution measured 15% fewer tokens than GPT-5's tokenizer on legal German, while tying it in English.

**The attention.** Only 10 of Kolibri's 50 layers process the full context; the other 40 use a tight 512-token sliding window, which bounds decode cost regardless of sequence length. Crucially, positional information lives only in the sliding-window layers, so context extrapolates past the trained 256k to 1M without extra position tricks; the base model scores 63.2 on RULER at 1M tokens.

**The data.** The German pipeline had to be retuned: the standard "remove documents with too many long words" filter quietly deletes the register public administration writes in. And rather than translate English, Aleph Alpha rephrased its own German corpus into encyclopedic and Q&A forms — about 1T tokens, the single largest German source — because translation carries cultural context with it: a corpus translated from English ends up speaking German about a world that looks American.

**The honesty.** Kolibri is trained to abstain. Their Merlin-Arthur protocol runs a three-player game: Merlin builds easier contexts, Morgana strips out the evidence to bait a hallucination, and Arthur — the model — learns that abstention is the only winning move against the redacted context. Kolibri abstains instead of answering wrong on 44% of AA-Omniscience items (Origin: 15%).

Two routing choices are worth noting: 384 narrow experts (6 active, plus one shared) rather than fewer wide ones, and *exact* quantile balancing. Kimi K3 introduced it with a histogram approximation; Aleph Alpha shows the exact computation is possible at fixed cost, improving load balance and quality.

It is not the best at everything: last of twelve models on closed-book knowledge, weaker at multi-turn tool calling and coding (27.7 on Terminal-Bench 2.1), and it needs ~78&nbsp;GB of GPU memory despite the 3.5B active parameters.

## Impact

Kolibri is a reminder that "sovereign AI" is not a label but a set of unglamorous engineering problems — tokenizers, filters, cultural grounding, abstention — that a frontier race tuned to English benchmarks has little incentive to solve. For a European ministry, hospital, or manufacturer that must keep documents in-house and wants answers that reason in German, a model that scores 70.8 on their German suite and says "I don't know" is more useful than a larger one that confidently invents. The practical path is an OpenAI-compatible vLLM server via Aleph Alpha's plugin — with, at launch, exactly one supported vLLM version.

The strategic read is sharper: as open weights commoditize general capability, differentiation moves to specialization you can measure and control. And the boring layers — how text is split, filtered, and grounded — are where it gets won.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Aleph Alpha — Kolibri Has Landed](https://aleph-alpha.com/en/blog/kolibri-has-landed-a-sovereign-open-weight-model/) & [Tech Report](https://aleph-alpha.com/downloads/tech-report.pdf) & [Tejas Kumar analysis](https://tej.as/blog/aleph-alpha-kolibri) | HN Discussion: [510 points, 298 comments](https://news.ycombinator.com/item?id=49942706)*
