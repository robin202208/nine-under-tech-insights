# Navier–Stokes Lost in Translation: When a Lean Proof Is Not the Proof

> A new paper shows that Lean-verified autoformalisation can silently rewrite a proof — and proves that closing the gap in general is harder than the Halting problem.

## What Happened

On October 6, Alexander Bastounis (King's College London) and Fabian Circelli and Anders C. Hansen (University of Cambridge) posted *Navier–Stokes lost in translation*, a 25-page analysis of how AI-generated mathematics gets verified. Their target is **autoformalisation**: an AI translates a natural-language (NL) proof into Lean, a proof assistant, and the machine mechanically checks the result. OpenAI used exactly this pipeline for its announced finite-time blow-up proof of the Navier–Stokes equations, publishing a Lean 4 repository alongside the paper.

The authors compared that repository against the NL text — and the two do not match. In one case (equation 8.19, Lemma 8.6), the NL paper bounds a quantity using four extra derivatives (`m+4`), while the Lean theorem `norm_derivativeWord_inverse_le` requires five (`m+5`). The formal estimate is strictly weaker than the stated one. In another (equation 10.19), the NL pressure-flux bound depends only on one quantity, `B_R`; the Lean estimate additionally depends on `A_R` and swaps an exponent from `R^-3/4` to `R^-7/4` — a different bound, established by a substantively different argument. Preliminary checks suggest the companion Euler proof likely contains similar mismatches.

The paper explains why this is not mere sloppiness. When success is defined as "Lean accepts *some* proof of the already-translated theorem," the generation loop is free to replace a correct argument with a different valid one — or to repair an incorrect one — and call it success. Nothing in the acceptance criterion checks that the original reasoning survived the translation.

## Why It Matters

The deeper claim is computability-theoretic. Faithful translation requires resolving ambiguities in NL mathematics — deciding, for instance, whether an object is even well-defined. Using the Solvability Complexity Index hierarchy and Hilbert's tenth problem (for `k ≥ 9`), the authors show this ambiguity-resolution problem sits at SCI = ∞: **harder than any computational problem, including the Halting problem** (SCI = 1). Consequently, no autoformaliser can always know when it should translate and when it must refuse. A semantics-preserving autoformaliser does not currently exist, and the authors argue quick fixes will collide with limitations dating back to Gödel and Turing.

That reframes what "Lean-verified" means. It certifies that *a* proof exists — not that the human-readable proof is correct. Commenters on Hacker News were quick to draw the same line: nobody is disputing that the Lean proof compiles; the concern is that it is not the same proof as the paper. Back-translating Lean into English does not close the gap either, because the back-translation needs the same faithfulness guarantee that the forward direction could not provide.

The risks are not hypothetical. In spring 2026 Meta claimed to have autoformalised 26 mathematics textbooks into 45,000 Lean declarations. Lean experts dismantled the claim: in one textbook, "0% of the statements" were accurately formalised, and much of what was proved was already in Mathlib. Humans caught it this time. In a shared library like Mathlib, a single mistranslated statement can silently invalidate every result built on top of it — and human review will not scale to Anthropic's reported 13-million-line Lean formalisation of Fermat's Last Theorem.

## Impact

For teams shipping AI-for-math or verification pipelines, the lesson is to treat a successful Lean build as one artifact among several. Compilation answers "is there a valid proof?"; fidelity answers "is this the proof the author wrote?" Those are different bars, and only the second certifies a paper. Pipelines should log translation provenance, diff the NL argument against the formal one, and surface — rather than auto-accept — cases where the formaliser quietly changed a bound or switched strategies.

It also means "Lean-verified" should stop being marketed, benchmarked, or regulated as a synonym for "correct." Two 2026 declarations — the Leiden Declaration and a statement initiated by 25 Fields medalists — already warn that plausible-but-unreliable arguments are becoming hard to distinguish from real proofs. This paper supplies the mechanism, and its impossibility result says the fix is a research program rather than a patch.

The most valuable output here may be negative: a precise statement of what formal verification cannot promise. Autoformalisation can tell you that a theorem is provable in a formal system. What it cannot do, in general, is tell you that the proof you published is the proof that was checked.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [arXiv:2610.08144](https://arxiv.org/abs/2610.08144) | HN Discussion: [248 points, 156 comments](https://news.ycombinator.com/item?id=49994145)*
