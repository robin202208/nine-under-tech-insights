# AI Is Getting Dangerously Good at Killing Mathematical Conjectures

> In the span of two months, frontier AI models have independently found formal counterexamples to three major unsolved problems—including a 100-year-old conjecture in algebraic geometry—reshaping what it means to do mathematics.

## What Happened

On July 20, 2026, mathematician Levent Alpöge announced that Anthropic's Claude Fable had discovered a counterexample to the Jacobian Conjecture, a 100-year-old open problem in algebraic geometry first posed by Ott-Heinrich Keller in 1939. The conjecture—which asserts that every polynomial map with a constant nonzero Jacobian determinant is globally invertible—had resisted all attempts at resolution for nearly a century. The counterexample was formalized in Lean within hours by mathematician Paul Lezeau using DeepMind's Formal Conjectures repository, making verification a triviality.

This is not an isolated incident. Kevin Buzzard, professor of pure mathematics at Imperial College London, has documented a cascade of AI-driven disproofs over the past two months. On May 20, ChatGPT disproved Erdős' Unit Distance conjecture using a profound theorem from number theory (later fully formalized by OpenAI's Sol model in 1.2 million lines of Lean). In early July, Sol found a counterexample to a 60-year-old question of Grothendieck about whether every finite free group scheme of order n is killed by n—a 1,076-line Lean proof that compiled in under five minutes. The Jacobian Conjecture is merely the latest and most prestigious scalp.

The Jacobian counterexample emerged during an extraordinary month. Buzzard's "Formalizing Fermat" workshop (July 6–10) gave 25 mathematicians simultaneous access to Claude Fable, ChatGPT Sol, and Logos' autoformalization tools. PhD student Andrew Yang used these tools to write 250,000 lines of Lean in two weeks, completing a modularity lifting theorem crucial to Buzzard's ongoing proof of Fermat's Last Theorem. As Buzzard put it: "Any PhD student who was *not* paying $200 per month to access these tools was crazy."

## Why It Matters

The pattern reveals something subtle but profound: AI is not merely a faster calculator. It is exceptionally good at *searching* for counterexamples—a task that requires systematically exploring edge cases that human intuition tends to overlook. Human mathematicians, when presented with a beautiful conjecture, typically try to prove it. The cognitive bias is toward confirmation. AI has no such bias. It searches the space of possibilities without aesthetic preference, and it finds the ugly counterexamples that humans miss.

The Lean formalization pipeline is the key enabler. A counterexample is only as good as its verification, and the fact that these AI-generated proofs can be automatically compiled against mathlib—the community-maintained library of formalized mathematics—means verification becomes a five-minute compilation check rather than a months-long peer review. Buzzard's heuristic is now: "I am not reading AI-generated informal mathematics." Only the formalized Lean proof counts. This shifts the burden of trust entirely onto the formal verification system, which, unlike human reviewers, cannot be fooled by plausible-sounding prose.

## Impact

For working mathematicians, the implication is stark: any conjecture that has been formalized in Lean (or a similar system) is now exposed to automated counterexample search. DeepMind's Formal Conjectures repository, which already contains formalized statements of major open problems, effectively becomes a hit list. The Hodge Conjecture may be next.

For the broader AI community, this is a milestone in autonomous reasoning. These are not pattern-matching exercises or benchmark satisficing. Finding a counterexample to the Jacobian Conjecture requires constructing an explicit algebraic object with specific pathological properties—genuine mathematical creativity. That frontier models can do this, and that formal verification can close the loop without human intervention, represents a step change in AI's capacity for original scientific discovery.

For PhD students and research institutions, the economics are shifting rapidly. Harvard already provides free Fable access to all PhD students, post-docs, and faculty. The gap between those who can afford $200/month for frontier models and those who cannot is becoming a determinant of research output. Buzzard's advice is blunt but hard to argue with: the tools make you far more than 10% more productive.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Xena Project](https://xenaproject.wordpress.com/2026/07/20/human-mathematicians-are-being-outcounterexampled/) | HN Discussion: [661 points, 428 comments](https://news.ycombinator.com/item?id=48973869)*
