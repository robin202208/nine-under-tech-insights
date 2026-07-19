# AI Just Learned to Train Without Backpropagation — And It Worked on CNNs for the First Time

**2026-07-19** | Sakana AI | Deep Learning · Neuroscience · Hardware

---

## What

Every neural network you've ever used — from ChatGPT to your phone's face unlock — was trained with backpropagation, a 40-year-old algorithm. It works brilliantly. It also requires a mathematical operation that real brains almost certainly cannot perform: the "weight transport problem," where error signals must travel backward through exact transposes of every forward weight matrix. Francis Crick flagged this as biologically impossible in 1989.

On July 17, Tokyo-based Sakana AI (founded by ex-Google Brain researcher David Ha and Transformer co-author Llion Jones, valued at $2.65B) posted a paper titled **"Diffusing Blame"** that achieved something no one had before: training convolutional neural networks and reinforcement learning agents using a biologically plausible learning rule called Error Diffusion — without backpropagation, without random feedback matrices, and while strictly obeying **Dale's principle** (the biological rule that each neuron can only excite OR inhibit, never both).

The results: 96.7% on MNIST, 61.7% on CIFAR-10. On reinforcement learning tasks, their ED-PPO matched or exceeded standard backpropagation-based PPO on HalfCheetah (score 5,494 vs 3,520).

The architecture uses a dual-stream design — separate excitatory and inhibitory neuron populations per layer, with all trainable weights forced non-negative. Cross-stream connections carry a structural negation sign (not learned), so inhibition is hardwired. Error signals travel as a single global signed value routed to hidden units via deterministic modulo assignment — no weight transposition required.

---

## Why

This matters for three reasons that go well beyond academic curiosity.

**First, hardware.** Neuromorphic chips (Intel's Loihi 2, photonic neural networks, analog in-memory computing) encode synaptic weights as inherently non-negative physical quantities — light intensities, charge levels. Backpropagation-trained models can't run natively on this hardware because their weights freely mix positive and negative values. A Dale-compliant learning rule removes that barrier entirely.

**Second, the implicit compression.** Because weights are clamped to non-negative values, 37.3% of all parameters naturally converge to the minimum and become effectively pruned — with inhibitory connections reaching 68.8% sparsity. No explicit pruning step, no regularization. This "free compression" translates directly to lower power consumption on edge inference hardware.

**Third, the methodological warning.** Sakana's ablation study revealed something unsettling: the innovations that matter most on MNIST (sigmoid width tuning — 71.4-point accuracy collapse when removed) are almost irrelevant on CIFAR-10, where batch-centering dominates (47.9-point collapse). In other words, a learning algorithm optimized on one benchmark may be solving a fundamentally different problem than the same algorithm applied to a harder task. Single-benchmark evaluations are blind to this.

---

## Impact

The gap between Dale-compliant and standard training is now quantified: 7.4 percentage points on CIFAR-10 versus the best backpropagation-free alternative (DFA), which itself violates Dale's principle. That's the "biology tax" — the accuracy cost of playing by the brain's architectural rules.

But the trajectory matters more than the snapshot. This is the first time Error Diffusion — a 25-year-old algorithm that had never left binary classification and simple MNIST — has been demonstrated on convolutional networks and RL. The fact that a well-funded, credible lab published this as a genuine research contribution (accepted at ALIFE 2026) signals that biologically constrained learning is moving from neuroscience curiosity to engineering reality.

The practical near-term impact is clearest for edge AI. If you can train models that run natively on neuromorphic hardware with free sparsity and no backpropagation infrastructure, the power efficiency gains could reshape where inference happens. The longer-term question — whether this approach can scale to the complexity of modern architectures — remains open, but it's no longer unaskable.

---

*Source: [Diffusing Blame (arXiv:2606.31700)](https://arxiv.org/abs/2606.31700) — Sakana AI, July 17, 2026*
