# Multi-LCB: AI Coding Benchmarks Finally Go Polyglot — and the Results Are Uneven

> LiveCodeBench, the leading contamination-aware coding benchmark, just expanded from Python to 12 programming languages. The results reveal that LLMs are not nearly as language-agnostic as their marketing suggests.

## What Happened

A research team has released Multi-LCB, extending the widely-cited LiveCodeBench (LCB) from Python-only evaluation to twelve programming languages including C++, Java, JavaScript, Rust, Go, and Ruby. The paper, published on arXiv on June 21, preserves LCB's core innovation — contamination-aware evaluation using fresh competitive programming problems with known release dates — while answering a question the community has been asking since LCB became the de facto standard for coding benchmarks: do these models actually generalize across languages?

The methodology is clean. Multi-LCB transforms existing Python tasks from the LCB dataset into semantically equivalent tasks in other languages, preserving both the contamination controls and the difficulty calibration of the original. Each problem exists in all twelve languages, enabling direct cross-language comparisons on identical algorithmic challenges. The benchmark covers the full spectrum from C (the lowest-level language in the set) through systems languages (Rust, C++, Go) to high-level dynamic languages (JavaScript, Ruby, PHP) and JVM languages (Java, Kotlin, Scala).

The paper's headline finding: most LLMs show significant performance variance across languages, with Python consistently scoring highest and lower-level languages creating a steep drop-off. The gap between Python and C performance on the same algorithmic problems can exceed 30 percentage points for some models. Even models marketed as "multilingual" show surprising blind spots — a model that handles Rust well might stumble on Go, despite both being modern systems languages with similar design philosophies.

## Why It Matters

LiveCodeBench has become the benchmark that matters for coding AI. Its contamination-aware design — using recently released competition problems that couldn't have been in training data — made it substantially more trustworthy than HumanEval or MBPP, which are widely suspected to be contaminated. But LCB's Python-only scope left a critical blind spot: the assumption that Python performance generalizes.

Multi-LCB exposes that assumption as false. The variance across languages isn't random noise — it's systematic. Languages that appear less frequently in public code repositories show consistently lower scores. This has real implications for how we evaluate claims about "general coding ability." A model that scores 85% on Python benchmarks but 45% on C isn't "good at coding" — it's good at Python, which happens to dominate the training data.

The benchmark also reveals interesting patterns in how different model families handle language diversity. Some models show tight clustering across languages, suggesting genuine generalization. Others show high variance, suggesting heavy dependence on training data distribution. This distinction matters enormously for enterprise adoption: a company choosing a coding assistant needs to know whether it works for their stack, not just whether it aces Python.

The timing is significant. The AI coding assistant market is consolidating rapidly — GitHub Copilot, Cursor, and a wave of competitors are fighting for developer mindshare. Multi-LCB gives teams a way to evaluate claims about multilingual coding ability with the same contamination-free rigor that made LCB the standard for Python. Expect to see "Multi-LCB score" appear in model release blog posts within weeks.

## Developer Impact

For developers evaluating coding tools, Multi-LCB provides an immediate practical filter: if your team writes primarily TypeScript and Go, ask for Multi-LCB scores in those languages, not just Python. The benchmark's multi-language design makes it harder for model vendors to cherry-pick their best-performing language.

For model builders, Multi-LCB exposes a clear optimization target. The paper shows that some models achieve relatively flat cross-language performance — suggesting it's possible to build coding models that genuinely generalize. The techniques that produce this flatness (likely involving training data curation across languages) will become increasingly important as the market demands polyglot coding assistants.

The benchmark also has implications for how we think about AI-assisted software engineering education. If models are significantly worse at C and Rust than Python, students learning systems programming may get less reliable assistance — or worse, confident-sounding incorrect code — compared to their peers working in Python. Multi-LCB gives educators a data-driven way to set expectations.

## Our Take

Multi-LCB is the benchmark the coding AI community needed but didn't know it was missing. The finding that LLMs aren't truly language-agnostic isn't surprising — but having it quantified with contamination-aware methodology turns a suspicion into actionable data. The next question: how fast does that cross-language gap close as models improve?

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
