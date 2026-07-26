# Transformer MLPs Are Hebbian Memories — And You Can Build One With Zero Training

**July 26, 2026**

## What

Stanford's Hazy Research lab, led by Chris Ré, just published a paper that reframes how we understand fact storage inside Transformers. The core claim is deceptively simple: **a Transformer's MLP layer is, mathematically, a Hebbian memory** — the same associative memory principle that gave us the famous "neurons that fire together, wire together" rule from the 1940s.

The researchers go further: they provide a **closed-form construction** that builds a fact-storing MLP with zero gradient descent. Here's the recipe in one equation:

```
MLP(x) = B · φ(x),   where φ(x) = (Ax) ⊙ (Gx),   B = (1/F) Σ vᵢ φ(kᵢ)ᵀ
```

That's it. Sample two random Gaussian matrices A and G, compute the element-wise product of their projections as your feature map φ, then sum the outer products of value embeddings with those feature-transformed key embeddings. No backpropagation. No training loop. No GPU hours. Just linear algebra and a Hadamard product.

The classical Hebbian memory stores keys and values as W = Σ vᵢkᵢᵀ. Query it with q and you get Wq = Σ vᵢ⟨kᵢ, q⟩ — a weighted sum where similarity determines weight. The Stanford construction does exactly this, but measures similarity in the MLP's nonlinear feature space φ instead of raw embedding space. The gating (Hadamard product of two projections) provides the nonlinearity that makes the memory discriminative.

The result: facts are stored at the **information-theoretically optimal rate** of Θ(F log F) parameters for F facts. Prior empirical work had observed that LLMs approach this bound in practice, but nobody had explained *why*. This paper provides the first constructive proof.

## Why

This matters because it bridges two worlds that have been talking past each other. On one side, mechanistic interpretability researchers have been reverse-engineering how models store facts — poking at attention heads, analyzing neuron activations, building circuit diagrams. On the other side, associative memory theorists have been working with Hopfield networks and modern continuous memories, proving capacity bounds. This paper shows they were describing the same phenomenon in different languages.

The construction also exposes something uncomfortable: MLP fact storage is surprisingly simple. The random projection + Hadamard product trick is reminiscent of kernel methods and random Fourier features from the pre-deep-learning era. What the Transformer adds is scale — stacking these memories across layers so that later layers can retrieve facts stored in earlier ones, building compositional knowledge from atomic associations.

Perhaps most practically, **fact editing becomes trivial**. Want to update "the capital of France is Paris" to something else? You can surgically modify the B matrix without touching any other weights. No fine-tuning, no catastrophic forgetting, no ROME or MEMIT-style optimization. Just compute the contribution of the old fact, subtract it, add the new one. The paper demonstrates this with seamless edits directly inside Transformer blocks.

## Impact

The immediate consequence is for model editing and interpretability. If we can construct fact-storing MLPs by hand, we can also *read* them by construction — we know exactly where each fact lives because we put it there. This flips interpretability from a post-hoc forensic exercise into an engineering property.

Longer term, this suggests a different approach to knowledge injection in LLMs. Rather than hoping the model memorizes facts during pretraining or relying on retrieval-augmented generation at inference time, we could inject structured knowledge directly into specific MLP layers post-training. A medical LLM could receive a nightly update of the latest drug interaction data without retraining. A legal model could incorporate new case law as it's published.

The deeper philosophical implication: the line between "memorization" and "reasoning" in Transformers may be thinner than we thought. If MLPs are associative memories and attention is pattern-matching, then what we call "reasoning" might simply be the emergent behavior of retrieving and composing the right memories at the right time. The Hazy Research paper doesn't answer that question, but it gives us the mathematical language to ask it properly.

---

*Source: Garcia, Liu, Junkins et al. "MLPs are Hebbian Memories: A Simple Recipe for Fact-Storing Transformers." Hazy Research / Stanford, July 2026.*
