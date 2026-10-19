# The Cat-and-Car Neuron Was a V: Why Neural Networks May Already Be Clean

> A 65-minute working hypothesis from Goodfire's Nick Cammarata argues that polysemanticity — the field's main reason for distrusting individual neurons — was mostly a measurement error, not superposition.

## What Happened

Nick Cammarata, an interpretability researcher at Goodfire and co-author of the Distill essays that helped define the field (*Zoom In*, *Multimodal Neurons*), published **"Neural Networks Are Already Clean"**, a long research essay he labels a working hypothesis. The thesis: the evidence that convinced mechanistic interpretability that networks pack unrelated concepts into single neurons — *superposition* — was misread. Networks, he argues, use one learning principle, generalization. The tangling researchers attributed to a second principle was an artifact of immature tools and unfixed architectures.

The showcase is InceptionV1's neuron 4e:55, the canonical "cat and car" polysemantic neuron cited from 2017's *Feature Visualization* through 2020's *Zoom In* as proof that single neurons are not a sensible unit of study. Re-reading thousands of its activations, Cammarata found the shared feature was neither cats nor cars: it is a **V shape**. The neuron fires on pointed things (fox snouts, flames, martini glasses) and is pushed negative by round ones (golf balls, clocks). The whiskers and chrome that once looked like an arbitrary cat/car mixture came along for the ride through high-level generalization. The same applies to the 2019 circuit that became the field's superposition poster child — a car feature appearing to leak into unrelated dog neurons. It was shared **pose**; InceptionV1 contains more than a hundred generalized pose neurons, and a known bias in feature visualization produced the misleading render.

The numbers are what make this more than a re-reading. Retraining InceptionV1's original architecture with ordinary modern regularization — same size, same data, no interpretability in the loop — takes its final layer from "essentially no interpretable neurons" to roughly 99% legible. With better tools on the original 2014 checkpoint it is about 50%; retraining the same architecture, about 70%; a same-sized ConvNeXt reaches ~99%. For language models, preliminary audits of **Qwen3-32B** estimate that about 90% of neuron poles have one main job, ~7% have two related jobs, and only ~2–4% remain unexplained, with similar results up to trillion-parameter models. For comparison, Anthropic's 2023 *Towards Monosemanticity* gave the median neuron 0 out of 14, and OpenAI's automated neuron explainer found about 1.7% interpretable.

He names the residual motif **recursive generalization**: a neuron grows like a branch sprouting sub-branches. An "iy" neuron extends to "diy", then to "do it yourself", which contains no "iy" at all. The grouping is a clue about what the network considers similar, not arbitrary packing.

## Why It Matters

Since 2022 the dominant interpretability program has rested on a premise: you cannot study a network directly, so you first train a decomposition — a sparse autoencoder or dictionary-learning model — to approximate the network "unfolded", then study that instead. If superposition is the wrong explanation, that premise weakens. Cammarata's "bitter lesson for interpretability" is that scaling ordinary investigation beats engineering a clever new decomposition — the same pattern that made simple models at absurd scale win in deep learning.

He also argues the usual ordering is backwards: understanding the whole network is easier than understanding only the mechanisms you care about. A narrow target is one row of an empty crossword; once the map fills in, every piece constrains the next. Crucially, the fixes that made deep layers legible — residual connections, LayerNorm — were introduced to help models *learn* better, not to help interpretability. Legibility looks like a side effect of good architecture.

## Impact

If the hypothesis holds, the implications for safety work are concrete. Auditing alignment-relevant microstructure — the internal building blocks behind behavior like deception — becomes cheaper through direct neuron-and-circuit study: no second model to train, no proxy to trust. It also scales with model size instead of fighting it, since larger models tend to learn narrower, more recognizable neurons. The essay surfaces findings a decomposition would likely have hidden — for example, a double-negative regime in SwiGLU neurons that, as far as the author knows, no one had noticed.

The caveats are real. Cammarata explicitly frames the essay as a working hypothesis: the percentages move with where you set thresholds for "one thing" and weak activations, the samples are small, and the detail sits in a companion paper not yet released. Independent replication on open models is the test. But the direction of the claim — that the field's most-cited counterexamples to neuron-level interpretability were artifacts of the microscope — is the kind of revision that reorders a research program.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Neural Networks Are Already Clean — Goodfire](https://already-clean.goodfire.com/) | HN Discussion: [501 points, 195 comments](https://news.ycombinator.com/item?id=50105647)*
