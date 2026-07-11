# An AI Just Proved a 50-Year-Old Math Conjecture in Under an Hour

> GPT-5.6 Sol Ultra generated a three-page proof of the Cycle Double Cover Conjecture — a graph theory problem that resisted human mathematicians since 1973. The proof is concise, elegant, and appears correct.

## What Happened

On July 10, 2026, OpenAI researcher Edward Knight shared that GPT-5.6 Sol Ultra had produced a valid proof of the Cycle Double Cover Conjecture. First posed by George Szekeres in 1973 and independently by Paul Seymour in 1979, the conjecture asks a deceptively simple question: for any bridgeless graph, can you find a collection of cycles such that every edge is covered exactly twice?

The problem sits at the intersection of graph theory and topology. It resisted decades of concerted effort, generating thousands of papers on partial results and special cases. The reduction to "snarks" — non-3-edge-colorable cubic graphs — narrowed the battlefield, but the core remained unsolved. Multiple claimed proofs had been posted to arXiv over the years, all later retracted or found to contain fatal flaws.

GPT-5.6 found a concise three-page proof exploiting properties of the line graph operator combined with the Jaeger-Kilpatrick 8-flow theorem. The proof reads like a classic mathematics paper — lemmas building cleanly toward a resolution, each step verifiable by domain experts. The entire process, from prompt to completed proof, took under one hour.

OpenAI released both the proof and the prompt. The prompt spans multiple pages of meta-strategies, instructing the model to "reject status reports and vague optimism," explore parallel proof attempts, and assume a proof existed — counteracting the known tendency of LLMs to prematurely conclude a problem is intractable.

## Why It Matters

This is not a counterexample or a computational verification — it is a genuine proof. Earlier this year, GPT-5 found a counterexample to the Unit Distance Problem, but that was a search problem dressed up as discovery. A proof of an open conjecture requires constructing a logical chain from axioms to conclusion, closing a gap that human mathematicians had stared at for half a century.

The conciseness of the proof is both its strength and a source of unease. HN commenters noted it exploits "a clever trick that somehow all the experts missed" rather than building new theory. In mathematics, theory-building proofs — like Wiles' proof of Fermat's Last Theorem, which created entire new fields — are valued more than the result itself. But this critique may miss the point: if a machine can find the "clever trick" that eluded humans, it is doing something genuinely new.

The prompt engineering aspect is also significant. The model did not autonomously tackle the conjecture; it was steered with heuristics about search breadth and persistence. One commenter compared this to early chain-of-thought prompting — we are still telling models *how* to think about hard problems. But the result stands regardless of the scaffolding.

## Impact

As of this writing, no mathematician has publicly identified a flaw in the proof, and multiple AI-assisted checks using Claude and GPT-5.6 itself have returned cautiously optimistic verdicts. Formal verification with proof assistants like Lean remains challenging for graph-theoretic results, so the mathematical community's peer review will be the ultimate arbiter.

Beyond this single result, the proof demonstrates that frontier AI models have crossed a threshold. A model that can prove a 50-year-old conjecture can reason about problems whose solution strategy is not already in its training data. This has implications far beyond mathematics: drug discovery, materials science, algorithm design — any domain where the bottleneck is discovering a novel path through a complex space.

Rough estimates suggest the proof cost between $300 and $500 in inference compute, assuming 64 parallel subagents running for an hour. That is dramatically cheaper than five decades of human effort. If this ratio holds, the economics of mathematical research may shift rapidly.

Sam Altman had foreshadowed this moment days earlier, posting that he was "approximately as amazed" by GPT-5.6 discovering new mathematics as by his child's first two-word sentence. The comparison drew criticism, but the achievement is hard to dismiss. The question is no longer *whether* AI can do original mathematics, but how far it can go.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [OpenAI](https://cdn.openai.com/pdf/04d1d1e4-bc75-476a-97cf-49055cd98d31/cdc_proof.pdf) | HN Discussion: [337 points, 272 comments](https://news.ycombinator.com/item?id=48863490)*
