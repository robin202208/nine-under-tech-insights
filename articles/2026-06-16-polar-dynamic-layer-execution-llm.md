# LLMs Are Wasting Most of Their Layers on Every Input — And Researchers Just Proved It

**Date:** 2026-06-16
**Category:** LLM Architecture / Inference Optimization
**Source:** arXiv (2606.06574)

---

## What Happened

Every transformer LLM you've ever used — from LLaMA to DeepSeek — runs every layer, for every token, every time. It's the unspoken assumption baked into the architecture: 32 layers means 32 sequential computations, no exceptions. A new paper from Ziyue Li, Yang Li, and Tianyi Zhou calls this assumption into question with PoLar (Program-of-Layers), a method that discovers dynamic, input-dependent execution paths through a pretrained model's layers.

The core finding is stark: for most inputs, substantially shorter paths through the model achieve the same or better accuracy than the full forward pass. Even more surprising, incorrect predictions from the standard forward pass can be corrected by alternative programs that use *fewer* layers. The model's latent reasoning capacity, it turns out, extends far beyond what fixed-depth execution captures.

PoLar works by treating pretrained layers as reusable modules. A lightweight prediction network — trained on top of the frozen base model — learns to generate execution programs that skip or loop specific layers for each input. The base model weights never change. On mathematical reasoning benchmarks, PoLar consistently improves accuracy over standard inference while executing fewer total layers, and these gains persist under out-of-distribution evaluation.

## Why It Matters

The fixed-depth paradigm is a computational waste problem hiding in plain sight. Every "easy" token — punctuation, common words, straightforward completions — runs through the same expensive stack as the hardest reasoning problems. PoLar breaks this coupling. The model can take a short path for simple inputs and a longer one for complex reasoning, allocating compute where it actually matters.

This isn't just about speed. The paper demonstrates that fixed-depth execution captures only a narrow subset of an LLM's latent reasoning capacity. By exploring alternative paths through the same frozen weights — skipping some layers, looping others — PoLar surfaces correct answers that the standard forward pass misses entirely. The model knows more than it can express through its default execution path.

The training-free nature of the discovery is equally important. The authors didn't retrain or fine-tune the base model to find these alternative paths — they exist natively in pretrained weights. PoLar simply learns to route inputs through them, adding only a small prediction network whose cost is negligible compared to the base model.

## The Architecture

PoLar's design is elegantly simple. The prediction network takes the input embedding (or hidden states from early layers) and outputs a program: a sequence of layer indices that may skip some layers entirely or loop others multiple times. The base model then executes exactly that program — no more, no less.

Three insights make this work. First, layer granularity: packing layers into coarser modules avoids the combinatorial explosion of per-layer decisions. Second, program constraints: a maximum execution budget prevents the prediction network from simply choosing "all layers, always." Third, the prediction network is trained with a straight-through estimator, optimizing for final accuracy rather than mimicking the full forward pass.

On GSM8K, PoLar with 70% of the standard layer budget matches the full model's accuracy. With the full budget, it exceeds it — demonstrating that dynamic routing through pretrained weights beats static execution through the same weights.

## The Bigger Picture

PoLar points toward a future where "model size" stops meaning "fixed inference cost." If different inputs can take different paths through the same weights, the computational cost of serving an LLM becomes input-dependent — cheaper on average, with headroom for hard problems. This has massive implications for API pricing, edge deployment, and batch inference throughput.

The discovery also reframes what "reasoning" means in transformers. If multiple valid latent computations exist for the same input, then what we call "the model's output" is really just one sample from a space of possible computations. PoLar's prediction network essentially learns to search this space. This aligns with a growing body of evidence — from chain-of-thought to test-time compute scaling — that transformer LLMs contain more reasoning capacity than their default forward pass reveals. PoLar shows that you don't need more tokens or more layers to access it — you just need a smarter path through the layers you already have.

---

*Full paper: [Skip a Layer or Loop It? Learning Program-of-Layers in LLMs](https://arxiv.org/abs/2606.06574)*
