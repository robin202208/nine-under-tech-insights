# The 1.58 Bits Were a Lie: Intel Packs Ternary LLMs by Their Zeros

> Ternary models have been paying storage for equiprobable symbols they do not actually have. Intel measured 29 checkpoints and rewrote the layout — not the model.

## What Happened

A ternary LLM stores every weight as one of three symbols, −1, 0 or +1, scaled by a group-wise factor. Because three equiprobable symbols carry log₂3 ≈ 1.585 bits, the field has quoted "1.58-bit" since BitNet b1.58. Real packings never reach that: five trits fit in a byte (3⁵ = 243 ≤ 256), which is 1.6 bits, and because deployed quantizers use power-of-two group sizes, a 128-weight block needs ⌈128/5⌉ = 26 payload bytes — 1.625 bits per weight in production.

Intel's Evangelos Georganas, Alexander Heinecke and Pradeep Dubey (arXiv:2609.16338) measured the actual symbol distribution of 29 state-of-the-art ternary checkpoints — BitNet b1.58 2B4T, the Bonsai and MAPLE families, CAT-Q, ParetoQ, TriLM, BitCPM-CANN — and found zeros account for 29.7% to 51.5% of all weights. The sparsest case, CAT-Q Qwen3-1.7B at 51.48% zeros, proves the ternary alphabet is nowhere near equiprobable, and fixed-width packing was throwing that skew away.

Their layout, BITCOS, replaces the fixed-width code with a dense presence bitmap plus a compacted sign vector: the bitmap marks which slots are non-zero, the sign vector stores only the ±1s. Storage costs 2 − z bits per weight at zero density z, so sparser checkpoints get cheaper. BITCOS beats five-trit packing on 26 of 29 models and reaches 1.485 bits per weight on the sparsest — under the paper's own title barrier. Against the 2-bit format in production it cuts weight traffic by 1.16–1.32×.

What makes this interesting is that it is only a storage layout. It is bit-exact, needs no retraining, no sparsity hardware, and applies to existing checkpoints. Intel wrote unpack sequences for AVX-512, AVX2 and Xe2 GPUs, and benchmarked against LIBXSMM's 2-bit CPU kernel and XeTLA's int2 GPU kernel — baselines the paper describes as already roofline-optimal.

## Why It Matters

Decode at batch size one is bandwidth-bound, so bytes moved per weight is the budget that matters. Their roofline model makes it explicit: per 32 weights, BITCOS reads 4 bytes of bitmap + 4(1−z) bytes of signs + 0.5 bytes of FP16 scale, or 8.5 − 4z bytes. Whichever is smaller — a core's available bandwidth or the kernel's instruction cost — sets the time.

That model predicts wins and losses, and the measurements match. On bandwidth-rich parts (64-core Emerald Rapids, Arrow Lake P-cores) BITCOS converts its smaller payload into time: 1.14–1.28× and 1.13–1.27× faster GEMV than the 2-bit state of the art, with end-to-end vLLM decode over seven ternary models improving 1.10–1.18× and 1.02–1.15×. But on Lunar Lake, where eight cores share 108 GB/s, the kernel is instruction-bound: BITCOS sustains 28.9 GB/s against the 2-bit kernel's 74.7 GB/s and loses at every density. When cores are few, a cheaper decode with more bytes wins. That plainly reported negative result is the most useful part of the paper.

There is a timestamped irony: Bonsai 27B has the lowest zero density in the table (29.66%), and BITCOS stores it 0.96× — larger than five-trit packing. The newest, largest ternary models are getting denser, pushing against the trick just as ternary weights become a deployment story: Prism ML shipped Ternary Bonsai 2 27B this week at 1.76 effective bits per weight and a 5.9GB footprint.

## Impact

For anyone shipping local inference, the practical takeaway is that storage format is now an optimization surface independent of quantization quality. The same checkpoint can move 20–30% fewer bytes, purely from how symbols are encoded, and that converts directly into decode throughput on memory-bound silicon. Model cards should start reporting zero density next to bits per weight, because that single number decides whether a layout helps or hurts.

Hardware teams get a second signal. The fact that a data-dependent, variable-length layout decodes fast enough on AVX-512, AVX2 and Xe2 suggests ternary accelerators should spend their transistors on packing and unpacking, not just on making multipliers disappear. Prior kernel work already treats LIBXSMM and XeTLA as roofline-optimal; the remaining headroom is in the encoding.

Engineering culture gets the third lesson. The paper's gain comes from measuring deployed artifacts instead of assuming the textbook model, and it credits its own counterexample on Lunar Lake. Meanwhile HN commenters placed the obvious bets: some argued vector quantization and trellis methods dominate at this bit width, others read BITCOS as a transfer format rather than an in-memory one. Both are testable, which is the state a storage-format argument should be in.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Breaking the 1.58-bit Barrier for Ternary LLMs (arXiv:2609.16338)](https://arxiv.org/abs/2609.16338) | HN Discussion: [235 points, 37 comments](https://news.ycombinator.com/item?id=49732931)*
