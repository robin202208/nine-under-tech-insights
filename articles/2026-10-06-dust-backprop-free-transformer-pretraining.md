# Dust Trained Transformers Without Backprop — and Nearly Matched

> A research group replaced the chain rule with per-token random perturbations, and their method stays within reach of backpropagating a GPT-style language model from scratch.

## What Happened

In October 2026, a team at Q Labs Research (Samip Dahal, Bishwas Mandal, Serdar Gülbahar, and Akshay Vegesna) published Dust, a zeroth-order algorithm that pretrains transformer language models without a backward pass. Gradient-free training has long been judged incapable of scaling: a forward pass returns one scalar, so the variance of the gradient estimate grows with the number of perturbed dimensions, and with it the population needed for a usable update. Dust's move is to stop perturbing weights.

Instead it perturbs activations — node perturbation — independently at every token. Each token becomes a "virtual population member," so a single forward pass evaluates a population at least a thousand times larger than weight-space evolution strategies, which must materialize one perturbed copy of the weights per member. Each token's noise is rewarded by how much it lowered the loss at that token, plus, with a decay, at later tokens the jitter reaches through attention. Averaged over draws, the reward-weighted noise estimates the error at the layer's output, and its outer product with the layer's input is the weight gradient — the same outer product backprop forms, except the error comes from a population instead of the chain rule. To limit interference, layer types and attention internals are perturbed in separate passes that rerun only from the perturbed block onward, over a cached clean forward pass.

The team trained GPT-style transformers on FineWeb with a 4,096-token BPE tokenizer, token budgets from 100k to 20M, and populations from 64 to 16k. At 100k and 1M tokens, Dust ends below backprop. At 10M and 20M the gap shrinks as population grows; a power-law fit to the 20M ladder extrapolates a limit of 4.431 test loss against backprop's 4.633 — though the ladder was still falling at 16k draws, so the authors read it as evidence the gap keeps closing, not as a measured limit. The strongest weight-space baseline, EGGROLL, needs roughly 10^3 to 10^4 times the population to reach what Dust does at 64 draws. Dust also holds up under Adam, though modern optimizers were built for backprop gradients. Two results cut against conventional wisdom: larger models are more population-efficient, not less — a 243M-parameter model roughly matches one 120× smaller at population 256 and beats it elsewhere — and the cosine between Dust's estimate and backprop's gradient rises with population and stays aligned from 10M to 1B tokens.

## Why It Matters

Backprop is the only credit-assignment algorithm that trains modern neural networks, and deep learning's architectures, optimizers, and hardware have co-evolved around its requirement of differentiability. Dust is the first zeroth-order method competitive with backprop at the hardest task the authors could think of — pretraining a transformer — exactly the setting where gradient-free methods were expected to die. The result argues in the spirit of Sutton's bitter lesson: differentiability and backprop may be good inductive biases in the low-compute regime, but in a compute-rich regime they constrain which architectures can be trained at all. If generic search scales, it may eventually win the way self-play overtook bootstrapped learning in AlphaGo Zero.

The overparameterization finding reframes model size. Rather than an inefficiency to be pruned away, a larger model looks like a larger and better-conditioned search space — one that can put a large population to use. That is a different reason for scale than the usual capacity story, and it hints at why the alignment between Dust's estimate and backprop's improves rather than degrades with population.

There is also a biological angle: backprop is widely considered implausible in the brain, while node perturbation is not. And skipping the backward pass could in principle train models where backpropagation through time struggles.

## Impact

The authors are explicit about the limits. Dust is not compute-efficient enough to replace backprop today, and they did not train the new architectures it makes accessible; both are left to future work. The main experiments top out at 243M parameters and a 20M-token ladder, and the 1B-token run only measures gradient alignment. There is no independent replication yet.

What the paper does is shift what counts as plausible. If the compute-rich regime keeps arriving, a search-based credit-assignment algorithm stops being a curiosity and becomes a design axis — one that could pry architecture and hardware away from the differentiability constraint. For now, the honest reading is a foothold, not a replacement: a demonstration that "gradient-free cannot scale" was an assumption, not a theorem.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Q Labs Research — Dust](https://qlabs.sh/research/dust) | HN Discussion: [97 points, 17 comments](https://news.ycombinator.com/item?id=49970871)*
