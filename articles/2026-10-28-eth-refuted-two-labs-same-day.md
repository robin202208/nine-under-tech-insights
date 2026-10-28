# Two Labs, One Afternoon: Frontier Models Refuted the Exponential Time Hypothesis

> The hardness assumption behind hundreds of "optimal unless SETH fails" results fell in ten days — found by two model families on the same day, formalized in Lean, and now before the committee of the people who proposed it.

## What Happened

On October 26, OpenAI posted a 67-page preprint whose author line reads only "OPENAI." It gives a deterministic algorithm that exactly counts the satisfying assignments of an arbitrary *k*-CNF: for every fixed *k* ≥ 2, on *n* variables and *m* clauses, it uses polynomial space and runs in time `(n+m)^O_k(1) · 2^(n/(log n)^(1/2+o(1)))`. A count of zero decides satisfiability, so the Exponential Time Hypothesis — 3-SAT needs 2^Ω(n) time in the worst case — is refuted. The paper claims SETH, Gap-ETH, #ETH and NSETH fall with it. The previous best exact-counting bound was `poly_k(n,m) · 2^(n−Ω(n/k))`.

The same day, Anthropic posted its own (Hexagon 2610.00577): deterministic 3-SAT in 2^o(n), Lean 4 formalized, with a provenance note saying the core algorithm "was found on October 16 by an internal Claude research model with 14M output tokens and no human input." OpenAI says its models produced their proof the same day and formalized it the next.

The mechanism is algebraic, not combinatorial. Each clause becomes a polynomial equal to its truth value on Boolean inputs, and each variable occurrence is replaced by a linear form `⟨P_e,v(T), x⟩` whose coefficients are polynomials in *T*: at *T* = 0 it restores the original variable; at selected *T* = *t* the deformed factors separate by variable group once a few integer linear measurements are fixed. The count comes back by rational interpolation, and by Hermite interpolation over Hasse derivatives where values alone do not suffice. Sparsification, a three-occurrence bound, small-bias parity tests built as Tanner codes and variable copying extend it to arbitrary *k*-CNF. Deterministic, polynomial space: the usual escape hatch of randomization is unused.

Two days later, Shiva Kintali posted a 22-page simplification: for every fixed ε > 0, deterministic 3-SAT in `C_ε(n+m+1)^c_ε · 2^(εn)` bit operations — a claim that fits on one line.

Reception has been procedural. Anthropic mathematician Levent Alpöge suggested OpenAI's solution leaned on pre-existing work; hours later Anthropic published its own. Ryan Williams (MIT) is organizing the field's response with Russell Impagliazzo and Ramamohan Paturi — the two who introduced ETH. Williams reads the proof as mostly routine, except "two or three alien-looking parts."

## Why It Matters

ETH became the currency of fine-grained complexity. Hundreds of results read "optimal unless SETH fails": edit distance, longest common subsequence, regular-expression matching, Fréchet distance, graph-diameter approximation, treewidth parameterizations. If the refutation holds, the premise behind that genre is gone and a decade of "we cannot do better" has to be re-derived.

Not everything dies, and the paper says which parts survive. The LCS and regex lower bounds also follow from the Orthogonal Vectors Conjecture, which SETH implied but this result does not touch. Treewidth and diameter bounds rest on the hypothesis that unrestricted CNF-SAT — clause width growing with *n* — needs 2^(1−δ)n time; a family of algorithms whose constant depends on fixed *k* says nothing about that. P versus NP is untouched: 2^o(n) is not polynomial, and a subexponential *k*-SAT bound does not survive a polynomial blow-up in variable count.

For anyone building models, the sentence that matters is a line in a 2022 paper by Keles, Wijewardena and Hegde: self-attention is necessarily quadratic in sequence length "unless the Strong Exponential Time Hypothesis is false." That premise has just been challenged. Nobody gets a linear-time attention layer: the SAT constants are unusable at realistic *n*, and a fast attention algorithm would need a reduction into fixed-width SAT, the opposite direction. But an impossibility argument becomes an open problem, and the case for sub-quadratic architectures now rests on memory bandwidth and wall-clock measurements rather than a complexity proof.

## Impact

For the research pipeline: Lean formalization plus an external committee of ETH's own authors is now the bar for publishing a machine-produced theorem, and a 22-page human simplification is what makes 67 pages reviewable. Watch whether the Impagliazzo–Paturi–Williams group certifies it; until then, downstream papers are better relabeled "conditional on a hypothesis under dispute" than deleted.

For practitioners: nothing ships. The bounds are asymptotic, C_ε and c_ε are astronomical, and Anthropic states plainly that the result "says nothing about solving formulas in practice." Williams's forecast is two-sided — serious practical SAT progress within twelve months, or nothing.

The discovery event may outlast the theorem: two labs' models independently produced the same core algorithmic move on the same day, in Lean, with humans reduced to exposition and refereeing. If the mathematics survives review, the claim worth auditing is not the fallen hypothesis but the pipeline that produced it.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Sources: [OpenAI preprint](https://github.com/openai/math/blob/main/preprints/The-Exponential-Time-Hypothesis-is-False-October-25-2026/paper.pdf), [Anthropic — A 2^o(n) Time Algorithm for 3-SAT](https://hexagonmath.org/2610.00577v1), [Kintali's simplified proof](https://shivakintali.github.io/papers/ETH.pdf), [TechCrunch report](https://techcrunch.com/2026/10/27/frontier-ai-labs-say-their-models-refuted-the-exponential-time-hypothesis-what-does-that-mean/) | HN Discussion: [776 points, 282 comments](https://news.ycombinator.com/item?id=50251661)*
