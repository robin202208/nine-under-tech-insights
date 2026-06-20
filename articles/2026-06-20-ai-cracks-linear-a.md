# How One AI Engineer Used LLMs to Crack a 3,800-Year-Old Script

> A self-taught engineer combined Claude Code with seven years of linguistics study to decipher Linear A — a Bronze Age script that has resisted experts for over a century.

## What Happened

On June 19, 2026, AI engineer and amateur linguist Tom Di Mino published his claim of having deciphered Linear A, a Minoan writing system used on Crete from approximately 1800 to 1450 BC. The claim — currently under review by linguistics experts at Rutgers and Cambridge — represents the first credible attempt to systematically translate Linear A since the related Linear B script was cracked in 1952.

Di Mino's approach was both deeply human and aggressively computational. He spent seven years studying Linear A, made two trips to Crete, and built a suite of Python scripts using Claude Code to query, cross-reference, and organize the entire digitized Linear A corpus from the GORILA and SigLA databases. This allowed systematic hypothesis testing at a scale that would have been impractical for a lone researcher working manually.

The breakthrough came on May 22, 2026, when Di Mino identified a recurring prayer formula across five Minoan sanctuary sites. Within the formula, all words were known through their overlap with Linear B syllabic values — except the first word, which contained one unknown sign. By recognizing that this unknown sign functioned as "na," Di Mino unlocked the Semitic root N-W-Y ("to dwell"), a three-consonant verb pattern found throughout Hebrew and Akkadian. The prayer, once deciphered, mapped cleanly onto later Hebrew prayer structures — addressed to a goddess rather than a male deity.

## Why It Matters

Linear A has been one of archaeology's most stubborn unsolved problems. Unlike Linear B — which Michael Ventris deciphered in 1952 by recognizing it as archaic Greek — Linear A encodes an unknown language with no known descendants. Fewer than 1,500 inscriptions survive, and many are dry inventory lists. The phonetic values of roughly 40% of the signs were unknown, and 13 signs had no known sound value at all.

Di Mino's work proposes readings for 40 signs, resolves 13 previously-unknown phonetic values, and corrects 5 Linear B values that were still unresolved. His draft lexicon contains 408 translated Linear A terms, and he has identified the language as belonging to an extinct Semitic branch — a precursor to biblical Hebrew, akin to Latin's relationship with Italian.

The significance extends beyond linguistics. If validated, this demonstrates a new model for how individual researchers can tackle grand-challenge problems by combining deep domain expertise with AI-assisted systematic analysis. Di Mino didn't just ask an LLM to "translate Linear A." He used Claude Code to build custom tools that let him test linguistic hypotheses across the entire corpus — a workflow that mirrors how computational biology transformed drug discovery.

## Developer Impact

The technical approach matters. Di Mino's Python suite performed what amounts to an exhaustive cross-reference of syllabic patterns against known Semitic morphological structures — the kind of combinatorial search that human linguists have attempted manually for decades. This is pattern recognition at a scale where AI tools provide genuine leverage, not hype.

For developers building AI-augmented research tools, the lesson is clear: the value isn't in the LLM replacing the expert — it's in the LLM amplifying the expert's ability to test hypotheses. Di Mino's seven years of domain knowledge made the AI useful, not the other way around.

The stack is also instructive: Claude Code for scaffolding, digitized academic databases (GORILA, SigLA) as ground truth, and Python for the analytical pipeline. No exotic infrastructure — just a persistent researcher and the right tools.

## Our Take

If peer review validates even half of Di Mino's claims, this won't just be the biggest linguistics breakthrough in 70 years. It will be a case study in what happens when domain experts learn to build with AI rather than wait for AI to replace them. The amateur cryptographer who cracked Linear B in 1952 used index cards and pencil. His 2026 successor used an LLM-powered Python suite and seven years of obsession. The tools changed. The obsession didn't.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
