# VP ≠ VNP, Twice in 48 Hours: A 47-Year-Old Conjecture Falls to Tensor Geometry

> Valiant's conjecture survived from 1979 until Monday. Two labs published machine-checked separations within a day of each other — one of them found by a model no mathematician has audited.

## What Happened

On November 23 at 20:04 UTC, `anthropics/formal-math` gained a commit: "vp-vnp: VP != VNP, paper and Lean proof." The byline is "Claude, Anthropic," and the README is blunt: an internal research Claude model found the result, models wrote paper and formalization, and no human mathematician has checked the proof.

The theorem: an absolute constant `c > 0` such that over every field of characteristic `p`, for all large `m`, every arithmetic circuit computing the Hamiltonian cycle polynomial `HC_m` — and the permanent `PERM_m` when `p ≠ 2` — needs more than `m^(c·(log₂m)^(1/3))` gates. So VP ⊊ VNP over every field, and the bound survives for approximating circuits, putting VNP outside VP's closure. In characteristic two the permanent equals the determinant, which has polynomial-size circuits, so the theorem excludes that case — it breaks where the statement is false. It does not settle P vs NP, because arithmetic circuits must compute a formal identity over the field and all its extensions, not a function on `{0,1}`.

`openai/math` published its own manuscript the same day, dated — per `history.md` — to **November 6**. The next night brought family 379: seven papers on "Euclidean tensor geometry" its README says subsume the November 23 preprint, proving `VF ⊊ VBP ⊊ VP ⊊ VNP` over every field, with a grid state sum needing `n^Ω((log n)^(1/12))` gates, permanent bounds lifted to `exp(c(log N)²/(log log N)²)`, and a claimed `ω = 9/4` for matrix multiplication.

## Why It Matters

General arithmetic circuits resisted lower bounds for forty-seven years. The best bound for an explicit polynomial of degree `d` in `n` variables was `Ω(n log d)` (Strassen, Baur–Strassen); for the permanent, nothing past `m² log m`. Restricted models did far better — multilinear formulas, monotone, constant-depth — but those proofs bound ranks of partial-derivative matrices, which a general circuit's cancellations erase.

Both proofs borrow Strassen's spectral points, which assign a multiplicative price to tensors: a wire of dimension `q` between two parties has price `q^d`. Logs turn circuit size into length, `log φ(t) ≤` summed wire lengths, with rank across a split entering as one hub wire of dimension `L`; those distances obey the Bruhat–Tits inequality, so prices have nonpositive curvature and live in CAT(0). Then a dichotomy: either Euclidean positions exist, and a complete network of wires forces `log L ≥ ℓk^(3/2)/16`; or they do not, and a Poincaré inequality on upper unitriangular matrices over `F₂` becomes "rerouting laws" via Strassen duality, cutting any computation into short pieces until a small permanent circuit would accelerate them all — refuted by diagonalization.

OpenAI's statement of the invariant is the week's clearest sentence: a bit shared between two sites costs their Euclidean distance; one bit shared among `k` sites costs the normalized mean width of the convex hull of their positions, which for three sites is half the triangle's perimeter. That is why two labs converged inside a day — one idea, not two.

The permanent enters each case once via Valiant's completeness theorem; no such reduction is known for the determinant — also why characteristic two is exempt.

## Impact

Valiant's conjecture is now claimed resolved, with a caveat: nobody has read the proof, and `m₀` has no value because the contracting case quantifies over an integer `μ` it never pins down. What can be checked is: `Statement.lean` imports only Mathlib, `Challenge.lean` states the theorems unproved, and CI runs Comparator against it plus a second, independently written kernel, nanoda. The repo says plainly that Lean checks the proofs but no machine checks that the statement file says what the paper claims.

For geometric complexity theory this cuts both ways. The border form implies the permanent is not approximated by polynomial-size determinants — the Mulmuley–Sohoni statement — yet the proof uses neither representation theory nor orbit closures, and occurrence obstructions were already known to be incapable of superpolynomial bounds. The target was hit by a route that bypasses the plan.

Nothing here changes a stack or a bill. `NP ⊄ P/poly` plus a Riemann hypothesis for Dedekind zeta functions would imply VP ≠ VNP over `C`; the converse is unknown, so P vs NP is untouched. What changed is publication: priority is argued from commit timestamps and a changelog, both releases are paper plus Lean plus a checker, and review moved from "is the proof right" to "does the statement file say what the paper says." OpenAI's repository counts 761 preprints since September, including counterexamples to Kaplansky's and Hirsch's conjectures. The bottleneck is no longer ideas. It is readers.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [anthropics/formal-math — vp-vnp](https://github.com/anthropics/formal-math/tree/main/vp-vnp), [paper.pdf](https://github.com/anthropics/formal-math/blob/main/vp-vnp/paper.pdf) & [openai/math — VP and VNP are different](https://github.com/openai/math/tree/main/preprints/VP-and-VNP-are-different-November-23-2026) | HN Discussion: [357 points, 331 comments](https://news.ycombinator.com/item?id=50620102)*
