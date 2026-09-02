# A 67-Cent Transformer Beats LLMs on ARC-AGI — and Upends the Leaderboard

> A single researcher trains a small transformer from scratch for 1.5 hours on one RTX 5090 and scores 44% on ARC-AGI-1 for about 67 cents of compute — matching last year's prize-winning recursive models and beating many far larger LLM-based approaches.

## What Happened

Mithil Vakde, an IIT Bombay researcher, published the third post in his ARC-AGI series documenting a striking result: a small transformer trained entirely from scratch — 1.5 hours on a single RTX 5090, roughly 67 cents of rented compute including all inference — scores 44% on the ARC-AGI-1 public evaluation. That ties the performance of TRM and HRM, the recursive "test-time training" models that topped the 2025 ARC Prize leaderboard, and beats many much larger LLM-based systems. The same model scores 7% on the harder ARC-2.

The recipe is deliberately plain. Each input–output grid pair is serialized into a token sequence, and the transformer is trained autoregressively at test time on both the training and eval puzzles, with the test labels held out. Two representation choices carry most of the weight: a learned per-task additive embedding, and 3D rotary position embeddings that jointly encode the two spatial axes of each grid. Removing either collapses the score from 44% to roughly 24–25%, and downgrading 3D RoPE to 1D costs the same. Color and dihedral augmentations are applied during training and inverted at inference, and the two most common outputs are submitted (AAIVR).

The biggest gains came from modern plumbing: SwiGLU gated FFNs, RMSNorm, a WSD learning-rate schedule, and the NorMuon optimizer, which fixed a convergence stall the author saw with vanilla Muon. Switching from an unsupervised input+output objective to a supervised objective over output tokens alone lifted the score from 40% to 44% — a result Vakde admits he does not fully understand. Training data was extended with ARC-2 puzzles, carefully filtered to remove the 773 puzzles that overlap ARC-1 and would constitute a data leak; without that extra data the model still scores ~40% at roughly double the compute.

## Why It Matters

The result directly challenges the dominant narrative about what makes reasoning models work. TRM and HRM attributed their success to recursive loops and deep supervision; Vakde reaches parity with a single forward-function transformer and no recursion, arguing that the only confirmed benefit of recursion is trading memory movement for compute. He also calls out the "7M parameter" branding of TRM and HRM as misleading, noting that O(100M+) embedding weights are trained underneath — "7M active weights" would be the honest label.

More fundamentally, this is a sample-efficiency argument. ARC-AGI is a meta-learning benchmark with only about 1,000 puzzles, and every concept needed for the eval set appears in the train set. Frontier LLMs have saturated ARC-1 and ARC-2, but Vakde's analysis of the leaderboard suggests those scores are driven almost entirely by post-training on synthetic ARC-like data — base models remain stuck in single digits on ARC-2. A model that reaches 44% with 67 cents of compute and no offline pretraining demonstrates how far pure deep learning plus the right inductive biases can go, and how much of the LLM leaderboard is benchmark-fitting rather than general abstract reasoning. It also revives transductive learning, a paradigm studied since Vapnik's era, as a legitimate route to continual adaptation.

## Impact

If small models can hit competitive ARC scores on a single consumer GPU, the economics of reasoning research shift dramatically. Experimentation that once required frontier-scale clusters now fits into a researcher's afternoon on a rented 5090 — the author argues 65% is reachable within the transformer framework, noting that a union of solved tasks across multiple runs already reaches 55%. That democratizes participation in one of the field's most scrutinized benchmarks.

It also pressures benchmark governance. Vakde proposes banning offline pretraining and synthetic data — models must train from scratch after submission — arguing this guarantees fairness and turns the leaderboard into a true test of sample efficiency rather than a proxy for how much synthetic data a lab can generate. Whether or not organizers adopt the rule, the debate now has a concrete existence proof that such a regime is feasible. For practitioners, the lesson is broader: representation design and test-time adaptation may matter more than exotic architectures — the "next big thing" is often already sitting inside the obvious transformer you stopped considering.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Mithil Vakde's Blog — 44% on ARC-AGI-1 in 67 cents](https://mvakde.github.io/blog/44-on-arc-1/) | HN Discussion: [565 points, 149 comments](https://news.ycombinator.com/item?id=49519939)*
