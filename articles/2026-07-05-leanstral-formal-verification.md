# Leanstral 1.5: Mistral Just Made Formal Verification 75× Cheaper — and Found Real Bugs Nobody Noticed

**What**: Mistral AI released Leanstral 1.5, an Apache-2.0 licensed theorem-proving model purpose-built for Lean 4. With 119B total parameters but only 6B active (a sparse MoE), it saturates miniF2F at 100%, solves 587 of 672 PutnamBench problems, and achieves state-of-the-art on FATE-H (87%) and FATE-X (34%). More importantly, it does this at roughly **$4 per problem** — compared to Seed-Prover 1.5's estimated $300+ per problem. That's a 75× cost reduction for formal mathematical reasoning.

The model was trained in three stages: mid-training on proof data, supervised fine-tuning, and then reinforcement learning with CISPO (Contrastive Iterated Self-Play Optimization) across two distinct environments. In the multiturn environment, it submits proofs, receives Lean compiler feedback, and iterates until success or budget exhaustion. In the code agent environment, it operates like a human developer — editing files, running bash, querying the Lean language server — enabling long-horizon tasks across millions of tokens.

The result is a model that exhibits clean test-time scaling: Pass@8 on PutnamBench climbs smoothly from 44 problems at 50K tokens per attempt to 587 at 4M. Leanstral doesn't plateau. It just keeps reasoning.

**Why**: This release matters because it transforms formal verification from a research curiosity into a practical tool. The team built an automated pipeline — Rust-to-Lean translation via Aeneas, then Leanstral attempts to prove correctness properties. Across 57 real open-source repositories, it flagged 47 violated properties, 11 pointing to genuine bugs, **5 of them previously unreported on GitHub**.

One example: the `datrs/varinteger` library's zigzag decode `sign` function. On input `U64.MAX`, `(value + 1)` overflowed — crashing in debug mode, silently corrupting in release. Traditional testing and fuzzing missed it. A formal proof caught it automatically.

Leanstral also proved O(log n) time complexity for a real AVL tree implementation — structural induction across 2.7 million tokens and 22 context compactions, systematically unwinding a monadic time-tracking layer. These aren't toy problems. They're the kind of verification that matters for cryptography, safety-critical systems, and chip design.

The economics are equally compelling. At $4 per problem, formal verification becomes viable for projects that could never justify $300/problem workflows. Combined with Apache-2.0 licensing and availability as both downloadable weights and a free API, Mistral has lowered every barrier simultaneously: cost, licensing, and deployment friction.

**Impact**: Leanstral 1.5 signals a broader shift in what AI can contribute to software quality. We're moving past "AI writes code faster" toward "AI proves code correct." The feedback loop is uniquely clean: a proof either compiles under Lean or it doesn't. No benchmark ambiguity, no "helpful vs harmful" trade-offs. Just machine-checkable truth.

For developers, the immediate implication is that formal methods are no longer reserved for Boeing and NASA. A 6B-active-parameter model running at consumer prices can now find bugs that escape fuzzing and human review. For the AI research community, the message is that specialization works — a model tuned for one formal language and one proof assistant can outperform general-purpose models 10× its size.

The question isn't whether we'll have bug-free software. It's how quickly teams integrate proof-capable models into their CI pipelines. Mistral just made that question urgent.
