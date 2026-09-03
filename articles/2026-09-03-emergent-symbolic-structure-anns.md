# Neural Networks Are Secretly Symbolic: A Closed-Form Equation Can Replace an LLM's Internals Without Breaking It

> New research shows the vector representations of neural networks — including seven large language models — can be swapped for interpretable, symbol-structured equations while behavior barely changes. The oldest debate in AI may have been a false dichotomy.

## What Happened

The strongest AI systems represent everything as continuous vectors, yet they excel at domains that have been modeled for millennia as manipulation of discrete symbols — grammar trees, logic formulas, arithmetic. On August 30, researchers from Yale, Johns Hopkins, NYU, and Microsoft Research released a preprint ([arXiv:2608.29530](https://arxiv.org/abs/2608.29530)) proposing why: neural networks implicitly realize symbolic structure inside their vector representations, even though no one designed them to.

The team, led by R. Thomas McCoy, Tal Linzen, and Paul Smolensky — the cognitive scientist who proposed Tensor Product Representations (TPRs) in 1990 — used a method called DISCOVER. A TPR encodes a symbol structure by binding each element (a "filler") to its position (a "role") via a tensor product: in *cats chase dogs*, *cats* is bound to the subject role, *dogs* to the object role. DISCOVER trains an explicitly TPR-structured model to mimic a black-box network's hidden representations, then feeds the TPR equation's output into the original model's decoder. If the model still produces correct answers, its entire representation-generating process has been replaced by a human-readable closed-form equation — a proof of functional equivalence, not just correlation.

The result held across scales. On list-manipulation tasks (copying, reversing, interleaving), 600 DISCOVER models were fit over four architectures — MLPs, GRUs, Transformers, and a deliberately "bottlenecked" Transformer — and bidirectional-role TPRs approximated nearly all of them above 99% accuracy (worst case: 0.973). Structure-free baselines failed, and success was not a parameter-count artifact: Wickelroles, with 729 possible roles, lost to bidirectional roles with just 21.

Then the same pattern appeared in seven LLMs — Gemma-3-27B, GPT-2-XL, GPT-OSS-20B, Pythia-12B, Qwen3-14B, OLMo-2-13B, and Llama-3.1-8B. A deep analysis of GPT-OSS across arithmetic, logic, computer code, and language found TPR structure in all four symbolic domains. Partial code is on GitHub ([tommccoy1/discover](https://github.com/tommccoy1/discover)).

## Why It Matters

This is a direct strike at the "binding problem" — the classic objection that vectors cannot represent structure because addition is order-insensitive: if *cats chase dogs* and *dogs chase cats* sum to the same vector, no purely linear code can tell them apart. TPRs were proposed in 1990 as a *prescription* for building symbolic machinery into neural nets. This paper supplies evidence they are also a *description* of what ordinary, gradient-trained networks converge to on their own.

The causal experiments are the strongest evidence. By editing a representation so the word *clever* moves from the "subject adjective" role to the "object adjective" role, the model behaves as though the input were *the doctor helped the clever lawyer* rather than *the clever doctor helped the lawyer*. The discovered structure is not an artifact of the analyzer — the model's behavior depends on it. The authors also show systematic generalization: after seeing *scientist* in many sentences but never as the subject, DISCOVER still handles *scientist* as subject, indicating composition of fillers with roles is rule-like, not memorized.

The deeper implication is that the decades-long war between symbolic AI and connectionism was framed wrongly. Symbols need not be implemented as explicit rules over discrete tokens; they can be implemented as geometry — tensor-product bindings inside high-dimensional space. Networks trained only on next-token prediction rediscover the very structures that classical AI tried to hand-code.

## Impact

For interpretability, this offers a unifying formalism that feature-sum accounts (including sparse autoencoders) cannot provide: linear collections of concept vectors describe *what* is present but not *how* elements are arranged. A TPR approximation yields an interpretable equation for an entire model, layer by layer.

For control, it suggests a new steering lever: targeted interventions at the role level — moving a filler between roles — can edit behavior more precisely than prompting or fine-tuning. Expect research on unsupervised role discovery and on scaling these analyses to reasoning traces and multimodal models.

For the field's self-understanding, it cuts against the "just next-token statistics" dismissal of LLMs and the opposing "scale dissolves structure" view alike. Even systems trained without any symbolic inductive bias reinvent symbolic structure — evidence that structure is load-bearing for intelligence, not vestigial. The question was never whether neural networks use symbols, but how — and now we have a concrete answer: as tensor products, hidden in plain sight.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [arXiv:2608.29530](https://arxiv.org/abs/2608.29530) | HN Discussion: [271 points, 101 comments](https://news.ycombinator.com/item?id=49531651)*
