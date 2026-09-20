# A System 1 Model That Was Already Open Source a Year Ago

> TypeSafe AI launched Jev this month as a new class of model that refuses to write sentences. An independent researcher says he published the same idea — with weights — in March 2025, and brought a scoreboard.

## What Happened

This week a Hacker News post titled "I built non-autoregressive decision models with RL a year ago" reached 1,076 points and 259 comments. Its author, Nandakishor Mukkunnoth of ConvAI Innovations, used it to launch Laya: a "System 1 decision engine" that answers typed questions over schemas and returns probabilities instead of text.

The argument is about provenance, not performance. Mukkunnoth says he published arXiv 2503.23303 in March 2025 on sequence-conversion trajectories, released weights on Hugging Face, shipped an open dataset and a PyPI package, and posted the approach to r/LocalLLaMA. A second paper, arXiv 2510.01237, formalized schema-based decisions guided by reinforcement learning in September 2025. Then in September 2026 TypeSafe AI — founded by Diogo Almeida, who worked on the instruction-following research behind ChatGPT — launched Jev, presenting non-autoregressive typed decisions as a new scientific breakthrough: "without technical papers, without open weights, and with zero open training datasets," as Mukkunnoth puts it.

Laya is engineered as a direct answer. Where Jev describes a parallel sampler over a proprietary stack, Laya runs bidirectional encoders — ModernBERT-large for English, mmBERT-base for more than 100 languages — with three decision primitives: select one option from a dictionary, place a state on an ordinal rubric, or return a calibrated P(true). Because the output space is probabilities and labels, the model cannot hallucinate, and malformed JSON is structurally impossible.

On the author's head-to-head board, Laya takes the typed-decisions benchmark at 0.766 against Jev's 0.727 — above the 0.735 teacher ceiling — AG News at 0.950 versus 0.910, and DAIR Emotion at 0.595 versus 0.480, where he notes Jev returned zero probability on 16% of items. Calibration error falls from 0.246 to 0.081. Latency is 32.8 ms for one question against 236–276 ms, and 72.3 ms for ten batched questions against roughly 1,500 ms serial — a 20x gap. Laya ships as Apache 2.0 safetensors; Jev is a metered API at $0.042 per million tokens.

## Why It Matters

The scoreboard is the least interesting part, partly because it is self-reported: every Laya number is measured by its author, and the Jev numbers come from TypeSafe plus the third-party studies (AbdelStark, nibzard) he cites. The real signal is the shape of the dispute. A frontier lab shipped a category-defining claim with no papers, no weights and no datasets; an independent researcher replied with an arXiv timestamp and an open checkpoint. Novelty in AI is now falsifiable in public within days of a launch.

More valuable still is the section Laya spends on its own ceilings, which is the most concrete public description of this architecture so far. Choice questions share a 192–256 token budget, leaving roughly three to four tokens per candidate — so at 77 options on Banking77, Laya collapses to 0.425 while Jev holds 0.870. The recommendation: keep choice schemas under 20 options, or go coarse-to-fine. Zero-shot, the base models score about 0.35 on typed-decisions, near random; the headline 0.766 comes from fine-tuning on the benchmark's train split. Calibration is not free either: base weights ship with raw temperature logits, and fitting one scalar temperature per question type cuts expected calibration error from 0.466 to 0.081. Multilingual routing falls back to script detection plus Latin stopword distributions, adding under 2% overhead, and preloading weights avoids a seven-to-ten-second cold swap when traffic alternates between languages.

## Impact

For developers, this is a self-hostable, air-gapped alternative to a typed-decision API, running in under 35 ms on commodity hardware. The practical instructions are buried in the limitations: schemas under 20 options, fine-tune rather than trust zero-shot, and calibrate temperature per question type. Treat it as a fast specialized classifier, not an oracle.

For labs, the episode sets a precedent that open-weight teams will happily enforce: ship a category claim without artifacts and expect a public prior-art response with a table attached.

For the category itself, the missing piece is now present — an open baseline anyone can benchmark, fine-tune and falsify. Whether System 1 decisions become infrastructure will be settled by numbers other people can reproduce, not by which lab said it first.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Laya — ConvAI Innovations](https://laya.convaiinnovations.com/) & [arXiv 2503.23303](https://arxiv.org/abs/2503.23303) | HN Discussion: [1076 points, 259 comments](https://news.ycombinator.com/item?id=49765348)*
