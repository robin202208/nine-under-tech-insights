# DeepSeek-V3 in 190 Lines: What Fits on a Laptop, and What Doesn't

> A pure-JAX file with no framework, no fused kernels and no cluster rebuilds the three architectural bets that made DeepSeek-V3 cheap to serve — and puts numbers on two of them.

## What Happened

On 13 November, Vaibhaav Tiwari published `deepseek-v3-jax`: the DeepSeek-V3 architecture in 190 non-blank, non-comment lines of pure JAX. No Flax, no Equinox, no PyTorch. Parameters are plain dicts of arrays; the model is functions you can read top to bottom. MIT licensed, with the checkpoint, tokenizer, corpus hashes and training logs released alongside it.

The model is 36.2M parameters, six layers, 256-token context, and pretrained from scratch on a MacBook Air M5 with 24 GB of memory. The corpus is 207 public-domain detective novels — Hammett plus roughly 200 Gutenberg crime novels — tokenized into 21.7M tokens with an 8,192-token BPE vocabulary. Training runs 12,000 steps (about 98M tokens, 4.5 epochs) in roughly 2.6 hours at a median 10.7k tokens/s. Validation loss falls from 5.02 at step 250 to 3.51; train loss ends at 2.81.

Inside those 190 lines are the parts that make V3 distinctive: Multi-head Latent Attention, which caches one compressed latent plus a single shared RoPE key per token instead of per-head keys and values; DeepSeekMoE with one always-on shared expert and 16 routed experts, top-4 per token; the auxiliary-loss-free balancing bias; a multi-token-prediction head weighted at λ = 0.3; and bf16 compute on float32 master weights.

The interesting output is the ablation. At step 2,000, per-expert token share with the balancing bias on lands between 5.7% and 6.9%, against a perfectly even 6.25%. Turn the bias off and the same step spreads across 0.09% to 18.6% — one expert starved, another hoarding a fifth of the tokens. Validation loss at that step: 3.921 with the bias, 3.923 without.

## Why It Matters

Three claims get tested here, and each one is usually asserted at a scale nobody can inspect.

First, MLA is an arithmetic win before it is a kernel win. In this configuration the cache shrinks from 480 to 144 numbers per token per layer. In the real V3 the same move takes 40,960 numbers down to 576, which at a 128K context across 61 layers in bf16 is roughly 655 GB of KV state versus roughly 9 GB. That ratio is why long-context serving is feasible at all, and you can verify it with a pencil.

Second, auxiliary-loss-free balancing is a routing intervention, not a regularizer. The per-expert bias is nudged down when an expert is overloaded and up when it starves, and it changes which experts are selected without touching their weights. The conventional auxiliary loss suppresses imbalance by bending the gradient, which is why it can cost language-modeling quality. V3 keeps only a tiny sequence-wise balance loss (α = 1e-4) to stop one sequence from piling onto a few experts. Load evened out; loss did not move.

Third — and this is the instructive part — every expert runs on every token, with the unselected ones zeroed out. The routing math is exact, but there is no sparse dispatch and no expert parallelism. The model activates 13.6M of 36.2M parameters per token; routed experts hold about 23.6M, roughly two thirds of the network idle for any given token. That gap is the difference between MoE as mathematics and MoE as systems: the paper's efficiency comes from not doing the FLOPs, and a 190-line reference deliberately does not model that.

## Impact

Reference implementations are verification instruments. When a lab's headline claim is a routing decision or a cache layout, a few hundred lines and one afternoon of laptop compute can check it. Papers describing FP8 training, DualPipe scheduling, node-limited routing and YaRN extension at 671B are not falsifiable by hobbyists; "the bias evens the experts for free" is.

The published rough edges are also useful. `jax-metal 0.1.1` only works with JAX 0.4.34, and gradient through 2-axis contraction fails on Apple's GPU, so the model is written to avoid that operation. bf16 matmuls measured 11.10 against 2.76 TFLOP/s for float32, about 4x. The same code runs unmodified on ROCm: an RX 7600 XT with 16 GB finishes the full run in about 50 minutes at 35k tokens/s, reaching 3.527 against 3.507 on the Mac. The author's own verdict is that the model needs more data rather than more steps — it has begun to memorize detective prose without learning to hold a plot together.

Treat small-scale reproductions as a filter, not a substitute. They tell you which ideas survive contact with a loss curve and which only survive contact with a cluster. Anything that cannot be demonstrated in 190 lines deserves the harder question: is the advantage in the idea, or in the infrastructure around it?

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [deepseek-v3-jax](https://github.com/Vaibhaav-Tiwari/deepseek-v3-jax) & [technical write-up](https://vaibhaav.in/writing/deepseek-v3-jax/) | HN Discussion: [272 points, 27 comments](https://news.ycombinator.com/item?id=50497589)*
