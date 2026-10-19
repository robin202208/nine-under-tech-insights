# Dropping the VAE: How Pyramid-JiT Trains Text-to-Image Models in Pixel Space

> Linum trained a decoder-only image model with no variational autoencoder at all — and matched a prior latent-diffusion checkpoint using 11× fewer samples.

## What Happened

On October 6, Linum co-CEOs Manu and Sahil Chopra published *Pyramid-JiT: Low-Res Drafts, High-Res Images*, releasing code and weights under Apache 2.0. The work attacks an assumption nearly every modern image generator rests on: that you must compress an image into a variational autoencoder's (VAE) latent space before a diffusion transformer denoises it.

Linum argues the latent approach has an empirical ceiling. Latent diffusion models (LDMs) typically cap token compression at roughly 16×16 pixels per token unless the autoencoder is heavily reworked — and a separately trained VAE adds hundreds of millions of parameters, its own reconstruction artifacts, and a fidelity bottleneck. Their previous model, JiT-DDT, moved the whole pipeline into pixel space and pushed compression to 32×32 pixels per token. Pyramid-JiT (P-JiT) goes further: it is a decoder-only pixel-space architecture with no encoder and no VAE.

The central trick is what they call K-Readout. Because the model is trained with x-prediction in pixel space, every intermediate transformer block is already implicitly predicting a version of the target image. So instead of building a separate decoder, Linum bolts small MLP output heads along the DiT trunk, each regressing the image at a different resolution: 128×128 after block 10, 256×256 after block 16, and the full 512×512 at the final head. This sharpens the gradient signal in early layers that adapt slowly, and forces the early trunk to commit to scene structure rather than detail.

The numbers are strong. Three readouts beat a single readout by 10.0% on training loss. On FD-DINOv2 (where lower is better), P-JiT scores 27.9 against JiT-DDT's 39.1 and their earlier latent model Linum v2's 79.9 — approaching the real-vs-real floor of 25.4. P-JiT reaches Linum v2's final score in 11.3× fewer training samples, and trains in 4.3× fewer GPU-hours at four times the pixels. The released checkpoint activates 2.18B parameters, runs a 22-block single-stream DiT at width 2,944, and conditions on a frozen Qwen3.5-4B text encoder.

Not everything improved. P-JiT is slightly behind on prompt-following benchmarks — 0.817 versus 0.855 on GenEval, driven by counting (0.56 versus 0.80) — which the authors attribute to under-training. They also tried widening the residual stream with gated residuals (DeepSeek's mHC, Kimi's Attention Residuals, Qwen's Gated Residuals) and multiple patchifications; the simpler baseline won.

## Why It Matters

The VAE has quietly been the weak link in generative media. It outsources the hardest part — deciding what visual information is worth keeping — to a separately trained, frozen module, then asks the generator to work inside whatever lossy space it produced. It is a major reason generated faces show unnatural streaks and an airbrushed "AI sheen": the generator can only render detail the VAE preserves. Doing everything in pixel space removes that ceiling and collapses two models into one.

The K-Readout technique is also more general than images. Intermediate supervision at multiple resolutions is a cheap way to accelerate convergence in any model trained with an x-prediction or flow-matching objective, and it is far cheaper than the alternative — running intermediate outputs through a several-hundred-million-parameter decoder. Attaching a handful of tiny MLP heads is nearly free.

Cost is the deeper motivation. Attention scales quadratically with token count, and video models are expensive precisely because a short 720p, five-second clip costs on the order of 110,000 tokens. Every token eliminated slashes both training and inference cost. Linum is explicit that text-to-image here is a testbed: the real target is a cheaper video model, Linum v3.

## Impact

For teams building image and video generators, this is a concrete argument to reconsider latent diffusion. Fewer tokens mean cheaper training and inference; a decoder-only pixel-space design eliminates the VAE and the encoder-decoder split entirely, simplifying the stack. Because code and weights ship under Apache 2.0 as a research artifact, others can fork the recipe rather than re-derive it.

The honest negative results matter too: where gated residuals and extra patchifications failed, and where prompt following regressed — the disclosure that makes an open release trustworthy. The practical caveat is that P-JiT is not yet a drop-in replacement for composition-sensitive tasks like counting; the authors plan to train it to convergence.

More broadly, the latent-versus-pixel debate is reopening as raw compute cost becomes the binding constraint on generative media. When the dominant expense is attention over tokens, the model that keeps the most aggressive token compression while holding quality wins. Pyramid-JiT suggests that model may not need a VAE at all.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Linum Field Notes](https://www.linum.ai/field-notes/pyramid-jit) | HN Discussion: [49 points, 17 comments](https://news.ycombinator.com/item?id=49983582)*
