# Everyone's Been Tuning LoRA Wrong — The Scaling Factor, Not the Learning Rate, Is What Actually Matters

**Date:** 2026-06-16
**Category:** Model Fine-tuning / Optimization
**Source:** arXiv (2606.12883)

---

## What Happened

Low-Rank Adaptation (LoRA) has become the de facto standard for fine-tuning large language models. But a new paper from Zicheng Zhang and 12 co-authors argues that nearly everyone has been misconfiguring its most important hyperparameter. The scaling factor α — typically treated as a secondary knob adjusted alongside the learning rate — actually functions as the dominant driver of optimization, producing gains that learning rate tuning alone cannot replicate.

The paper introduces a Signal-Drift framework to explain why. In LoRA, the low-rank decomposition matrices A and B each follow distinct dynamics during training: B amplifies the task-relevant signal, while A introduces drift from random initialization. The scaling factor α sits at the intersection, and the authors show that it affects the signal-to-drift ratio in ways the learning rate cannot. Increasing α amplifies the task signal without proportionally increasing drift — a decoupling that the learning rate, which scales both signal and noise together, fundamentally cannot achieve.

This leads to three concrete findings. First, LoRA's spectral suppression (the low-rank constraint itself) smooths the optimization landscape, making standard hyperparameters overly conservative — there's headroom that nobody was using. Second, α outperforms the learning rate for accelerating convergence because it selectively amplifies signal. Third, the optimal α follows a square-root relationship with the LoRA rank: α ≈ k√r, where k is roughly 8-16× larger than the conventional α = r heuristic. The common practice of setting α equal to the rank is systematically under-scaling by nearly an order of magnitude.

## Why It Matters

LoRA has an estimated millions of practitioners — from individual developers fine-tuning on single GPUs to enterprises serving fine-tuned models at scale. The paper's finding that the standard α = r heuristic is dramatically suboptimal means that a vast number of existing LoRA fine-tunes are leaving performance on the table. This isn't a marginal 1-2% improvement. The proposed LoRA-α framework, which simply restores α to its principled square-root regime, delivers consistent improvements across diverse tasks while streamlining hyperparameter search.

The practical implications are immediate. Most LoRA users run grid searches over rank and learning rate, treating α as an afterthought. This paper suggests flipping the script: fix learning rate at a standard small value, then tune α according to √r. The result is both simpler (fewer hyperparameters to search) and better (higher final performance). For fine-tuning at scale, where hyperparameter search cost can exceed training cost, this is a direct dollar savings.

The Signal-Drift framework itself is a contribution beyond the specific finding. By decomposing LoRA training dynamics into signal amplification (B) and drift introduction (A), the paper gives practitioners a mental model for understanding what goes wrong in LoRA fine-tuning and how to diagnose it. High drift means A is dominating; high signal means B is learning. α is the lever that tips this balance.

## The Numbers That Matter

The square-root law is the paper's most actionable result. For a rank-16 LoRA adapter: the conventional α = 16. LoRA-α recommends α ≈ 8√16 = 32 — double. For rank-64: conventional α = 64, LoRA-α recommends α ≈ 8√64 = 64 — equal at this point. For rank-4: conventional α = 4, LoRA-α recommends α ≈ 8√4 = 16 — quadruple. The under-scaling is most severe at low ranks, which are also the most commonly used in resource-constrained settings.

Across commonsense reasoning, mathematical reasoning, and instruction-following benchmarks, LoRA-α with fixed small learning rates matches or exceeds the best results from exhaustive grid searches that included α tuning. The key insight: α's effect is qualitatively different from the learning rate's, and using it correctly eliminates the need to push learning rates into unstable regimes.

## The Bigger Picture

This paper is part of a broader trend of revisiting the fundamentals of parameter-efficient fine-tuning as the practice matures from research curiosity to production infrastructure. Just as the original AdamW paper revealed that weight decay and learning rate are not interchangeable despite appearing so, this work reveals that α and learning rate serve fundamentally different roles in LoRA optimization.

For the fine-tuning ecosystem, the implications are clear: existing LoRA recipes need updating. Libraries like Hugging Face PEFT, which default to α = r, should consider adopting the square-root law as the new default. Individual practitioners can immediately improve their results by bumping α on their next fine-tuning run. And for the research community, the Signal-Drift framework opens the door to α-aware optimizers that dynamically adjust the scaling factor during training — an obvious next step that this paper makes possible.

---

*Full paper: [The Hidden Power of Scaling Factor in LoRA Optimization](https://arxiv.org/abs/2606.12883)*
