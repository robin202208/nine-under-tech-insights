# 17,374,215 Lines: The Riemann Hypothesis, Verified but Unread

> On November 20 a mathematician published a four-page note and a 239 MB tarball. The tarball is the proof.

## What Happened

On the morning of November 20, Levent Alpöge posted a four-page remark announcing that he, "working with Claude," had found a proof of the Riemann Hypothesis — and that the method extends to the Generalized Riemann Hypothesis and, he expects, to the Extended one. There is no paper. Not because he didn't write one, but because a literal write-up ran past 250,000 pages, which he calls "slop." What he published instead is the formalization: fourteen gzip parts at `alpo.ge/lean.tar.gz.part01` through `.part14`.

We downloaded and unpacked it. 239,275,554 bytes compressed; 1.03 GB of Lean source across 175,187 files; 17,374,215 lines; 751,497 `theorem`/`lemma` declarations. The build pins Lean 4.33.1 and one specific mathlib commit, raises `maxHeartbeats` to 4,000,000, and its entry point `Main.lean` proves five propositions, including `riemannHypothesis : RiemannHypothesis` and `DirichletLZerosOnLine` for primitive characters.

A lexical audit finds zero files containing `sorry` and zero `axiom` declarations — unusual at this scale, and consistent with Alpöge's claim that the argument was formalized "without assumptions." The decisive check is `#print axioms`, which requires a full build; the tarball ships no prebuilt artifacts.

The mathematics, per Alpöge's own twenty-step timeline (October 6 to November 16), descends from Stepanov's function-field proof. Multiplicity is hard over the integers, so the argument chains theta lifts between totally definite unitary groups, with Claude proposing a chain of length √X — each lift mimicking one unit of multiplicity. Terence Tao's initial read: it "appears to use the full power of modern developments on the Langlands program and arithmetic geometry."

It landed in a crowded 24 hours. That same day, Ravi Vakil posted Alessandro Masullo's all-human GRH proof: roughly 940 pages, vetted for months by Brian Conrad, released early — by Vakil's account — precisely because the AI announcement made waiting untenable. Tao catalogued three results from the Alpöge circle in one post: unforced Euler blowup, unforced Navier–Stokes blowup, and GRH.

## Why It Matters

The proof is now a build artifact. Seventeen million lines cannot be read by anyone; the only plausible reader is a kernel. So trust moves off mathematics and onto a program whose soundness is itself a research question — Lean's kernel has historically admitted proofs of `0 = 1`, and the honest response is a second, independently authored checker rather than a second opinion. Note also what "checked" now costs: 751,000 lemmas at a 4-million-heartbeat ceiling is a compute job measured in machine-hours, not an afternoon in a seminar room. Nobody has published a completed build of this artifact yet, which means the result is, for now, a claim about a file.

Verification effort now scales with machine output instead of human attention. Alpöge's timeline shows six weeks of model time producing a corpus that would take a research group years to write. The scarce resources are no longer proof ideas but checker diversity and reproducibility: pinned toolchains, pinned dependency commits, deterministic rebuilds, transcompilation into a second logic such as Metamath Zero. These are supply-chain properties, and the artifact gets them mostly right — the toolchain and mathlib revisions are pinned in a manifest. What is missing is the audit trail of which subgoals came from the model and which from the human.

The natural experiment is the human proof, and it does not restore the old picture. Readers working through Masullo's draft report 27 footnotes that say only "Return here," a two-page chapter that is supposed to deduce GRH from the trace formula and states no theorem, and eleven false statements already found in the appendices. So the useful asymmetry is not human versus AI authorship. It is checkable versus not-yet-checkable — and only one of the two artifacts can be mechanically exercised today.

## Impact

For anyone shipping agents, this is the code-review problem arriving in mathematics: when generation outruns reading, correctness migrates to gates. The engineering question becomes what the gate proves, at what cost, and against which checker — and the answer has to be written down before the artifact exists, not after.

Concretely, expect demand for proof-size diffs, lemma-level provenance, axiom and `sorry` audits as CI steps, and independent checkers run as a service. Expect, too, a new disclosure format: Lean-only release is the cheapest honest thing a lab can publish and the hardest thing for outsiders to audit, so disputes will shift from "is the prose right" to "is the kernel right."

And keep one habit from this week. If you publish machine-generated formal artifacts, pin the toolchain and every dependency commit, because the only thing that makes 17 million lines mean anything is somebody else being able to rebuild them.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Levent Alpöge's remark](https://alpo.ge/remark.pdf) & [Lean formalization](https://alpo.ge/lean.tar.gz.part01) & [Terence Tao](https://terrytao.wordpress.com/2026/11/20/unforced-euler-singularity-unforced-navier-stokes-singularity-and-the-generalized-riemann-hypothesis/) & [Ravi Vakil](https://math216.wordpress.com/2026/11/20/hurried-announcement-alessandro-masullo-grh/) | HN Discussion: [2,359 points, 1,598 comments](https://news.ycombinator.com/item?id=50574968)*
