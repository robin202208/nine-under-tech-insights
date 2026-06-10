# RoPE Just Got a Replacement — And It Learns What to Forget Per Channel

**June 10, 2026** | By Nine Under Places

---

## What

Tilde Research just introduced **Wall Attention**, a data-dependent positional encoding that replaces RoPE entirely — the first in years to show both stronger pretraining convergence *and* extreme length generalization without stacking RoPE on top.

The key insight is elegantly simple: borrow the diagonal forget gates from linear RNNs (Mamba, RWKV-7, GLA) and lift them into softmax attention. Each channel of the query-key dot product gets its own learned decay rate. Some channels forget fast (recency bias), others persist indefinitely (long-range memory).

The math: a diagonal gate `D_t = diag(g₁, …, g_d)` produces per-channel cumulative decay `F_ij,n = ∏ g_s,n` from position j to i. The attention score becomes:

```
score_ij = Σ_n F_ij,n × q_i,n × k_j,n
```

This is fundamentally different from previous gating-in-attention attempts. FoX (Forgetting Attention) applied a scalar gate as an additive logit bias — more like ALiBi. Wall applies a per-channel multiplicative rescaling — more like RoPE, but learned and content-dependent. Tilde also developed a general "induced action framework" that unifies FoX, PaTH, and Wall as special cases of lifting RNN structures to softmax attention.

The implementation is production-ready: open-source Triton kernels, GQA-compatible, Multi-head Latent Attention compatible, and a WallDecode kernel that matches FlashDecode (FA3) performance. KV-head gate tying (sharing one gate vector per KV head) is a free efficiency win with no performance penalty.

## Why

Positional encoding is one of those architectural choices that shapes everything a transformer can learn. RoPE has been the undisputed standard since 2023 — it's in Llama, Qwen, DeepSeek, Mistral. If you're building a transformer, you use RoPE. There hasn't been a serious challenger.

Wall changes that for two reasons.

**First, convergence.** At 400M parameters, Wall (NoPE) outperforms the RoPE baseline under *both* Muon and Aurora optimizers — the gains are orthogonal to optimizer choice. At 1B parameters trained on 70B tokens, Wall achieves a 0.01 nats gain over RoPE and defeats both FoX and Wall+RoPE. This isn't just a long-context trick; it improves pretraining from the start.

**Second, extrapolation.** Models trained at 4K context generalize to 160K+ tokens with zero degradation. On Needle-in-a-Haystack, Wall maintains near-perfect retrieval at 4× training context where all RoPE/FoX-based models collapse past 8K. On LongBench v1, Wall achieves SOTA across coding, summarization, and few-shot learning.

The mechanistic analysis is fascinating. Wall learns a bimodal gate structure: some channels have retention fixed at 1.0 (permanent, unconditional memory), while others are highly dynamic and respond to content boundaries. This is learned end-to-end — no architecture imposes it.

One caveat: unlike RoPE where partial application (e.g., only first 25% of dims) works fine, Wall degrades monotonically as you reduce gated dimensions. The gate needs all channels to work. But KV-head tying compensates for the parameter cost.

## Impact

This is a foundational architecture paper. If Wall holds up at larger scales — and the fact that it's production-ready with Triton kernels suggests Tilde is testing exactly that — it could become the new default for transformer LLMs in 2027.

The implications span:

- **Long-context AI**: Models that handle 1M+ tokens without fancy position interpolation or YaRN-style scaling. Just train at 4K and extrapolate.
- **Inference cost**: No position encodings to compute. Wall Decode matches FlashDecode speed.
- **Agent systems**: Per-channel forgetting means the model can learn what context to retain across very long agent trajectories — critical for real-world deployment.
- **Architecture research**: The induced action framework opens a systematic way to translate any RNN gate design into softmax attention. Expect more cross-pollination between the linear RNN and transformer communities.

Wall isn't a demo or a proof of concept. It's a paper with open-source kernels, GQA/MLA compatibility, and validation across multiple model scales and optimizers. The authors claim Wall is "sufficient for strong positional and temporal understanding" — meaning you don't need RoPE, ALiBi, or anything else on top. If that claim scales, every major model architecture gets simpler and stronger.

The real question isn't whether Wall works. The question is whether it works at 70B, 400B, and beyond. The kernels are ready. Someone just needs to run the experiment.

---

*Source: [Wall Attention: Length Generalization With Diagonal Gates](https://blog.tilderesearch.com/blog/wall-attn) — Tilde Research, June 2, 2026*
