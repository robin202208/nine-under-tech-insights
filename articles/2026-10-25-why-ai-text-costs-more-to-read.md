# The Density Tax: Why Frontier Models Write Sentences That Cost More to Read

> A psycholinguistics paper measures comprehension-hostile syntax across 14 models — then reproduces the whole pattern with one training knob.

## What Happened

On October 21, Thomas Hikaru Clark (NYU), Matias B. Martin, and Kyle Mahowald (UT Austin) posted [arXiv:2610.29162](https://arxiv.org/abs/2610.29162), a study that takes the vague complaint "AI text is hard to read" and turns it into measurable syntax. They built a corpus of LLM output across three settings — 1,428 replies from 120 conversations, agentic coding sessions on five repositories, and 980 essays — and scored it against human baselines: OpenSubtitles for chat, StackExchange for code, PERSUADE 2.0 student essays and Wikipedia for prose.

Seven features, all established in the psycholinguistics literature as reading-cost drivers, were extracted from spaCy dependency parses: unattested bigrams per 1,000 pairs, trigram perplexity, dependency length (standardized by sentence length), object relative clauses with and without an overt relativizer, stacked noun compounds, denominal verbs, and inanimate subjects paired with animate verbs. Fourteen models were tested, including Claude Sonnet 5 / Opus 5 / Opus 5.5 / Fable 5, the GPT-5.6 family (Luna, Terra, Sol), GLM-5, Kimi K2.5, Llama 3.1 8B, and Olmo 3 7B in base, instruct, and think variants.

The control is the sharp part. To rule out "the model just had different content to express," the authors round-tripped every sentence through NLLB-200 via Japanese — a paraphrase that preserves meaning but not the model's stylistic choices — and repeated the check with a Russian pivot and TranslateGemma 4B. Against those paraphrases, all nine frontier and near-frontier models over-use inanimate subjects and low-frequency n-grams; most also produce longer dependencies, more stacked nouns, and more reduced object relatives in chat. The effects are absent or small for Llama 3.1 8B and Olmo 3 base, and grow monotonically inside the Olmo family from base to instruct to think — with Olmo Think matching frontier-scale distortion. Claude Opus 5.5 is lower than Opus 5 on 19 of 21 difficulty-linked comparisons, eight with non-overlapping 95% intervals.

## Why It Matters

Human language is shaped by comprehension. Speakers minimize dependency length and spread information evenly, because the reader has a lossy, incremental working memory. Models inherit those statistics from pretraining — and then post-training overwrites them. RLHF kept humans in the loop and implicitly rewarded prose that read well; RLVR rewards only verifiable outcomes. Once correctness is decoupled from clarity, any syntactic variant that still passes the checker but costs fewer tokens is free to win, and there is no gradient pushing back toward readability.

The authors test that mechanism directly. Starting from Qwen2.5-1.5B-Instruct, they run GRPO on a JSON-to-sentence task where every item can be expressed two ways, one shorter by a known token count. At zero length penalty, seeds landed anywhere from 0% to 100% preference for the short form — no stable prior. With any positive penalty, all seeds converged above 98% on the short variant for the trained "that"-omission alternations, transferred to unseen "that" constructions, and (without KL regularization) even reached the genitive alternation. The model was never told to compress, never shown the alternations, and still solved the task — because both variants were valid answers.

That is the uncomfortable result: the compression preference is emergent, not instructed. It also explains why the drift is strongest in long agentic sessions, where the model has twelve turns and a token budget to optimize against, and weakest in single-shot essays. A context window is a budget, and the model learns to spend it densely.

## Impact

If density is an artifact of the reward function, readability becomes an engineering metric rather than a taste preference. Teams running post-training should score syntactic load — dependency length, n-gram surprisal against a reference corpus — alongside accuracy, the same way they now score verbosity.

Second, this is a monitorability problem before it is a prose problem. Agent transcripts, incident reports, and rationale traces written in compressions that humans parse slowly are harder to audit, and the models most prone to it are the ones doing the most autonomous work.

Third, model choice matters more than prompt engineering here. Family variation is large and measurable; version regressions are real, but so is the Opus 5 → 5.5 improvement. Benchmark candidates on your own corpus with a readability proxy instead of trusting vendor prose samples.

Prompts asking for plain language help at the margin, but the paper's framing suggests they treat a symptom. The pressure lives in the objective, and objectives are chosen deliberately.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [arXiv:2610.29162](https://arxiv.org/abs/2610.29162) | HN Discussion: [153 points, 148 comments](https://news.ycombinator.com/item?id=50223647)*
