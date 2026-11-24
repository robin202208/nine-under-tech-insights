# Three Bits, 150 Tool Calls: A Quantizer That Only Counts the Tokens the Model Writes

> A 27B coding agent compressed from 53.8 GB to 10.1 GB loses nothing on DeepSWE — because the loss stopped averaging over tokens the model never writes.

## What Happened

Nari Labs released NariQ on November 20: Qwen3.8-27B at 3.0, 2.5 and 2.0 bits per weight. The 3-bit file is 10.1 GB against BF16's 53.8 GB. On DeepSWE v1.1 — 113 long-horizon software tasks averaging 150 tool calls, 50 minutes and a 92K-token median context — it fully solves 47 against BF16's 44. Terminal Bench 2.1 scores 63.8 against 62.6, AIME 98.3 against 97.7, and vision understanding is unchanged.

Low-bit files normally do not do this. At or below 3 bpw, published quantizations of the same model solve at most 72% as many DeepSWE tasks as BF16; GSQ-RCO's 3.00 bpw file solves 29 (63%), EXL3's 2.20 bpw file 11 (24%). Both look fine on AIME and then lose a third of the agent.

The code is not the innovation. NariQ uses QTIP's bitshift trellis after randomized Hadamard rotations, with the codebook computed from the trellis state so kernels decode inside the matrix multiply — the family EXL3 already ships. The paper concedes the point: on Gaussianized weights, trellis codes land within about 10% of the rate-distortion bound at 2 bits. No codebook was going to close a 37-point gap.

What changed is the objective. The KL loss counts only model-generated tokens; agent traces are dominated by tool output and file contents, which drown the tokens that matter. Bits are then allocated per module from that module's own gradient-estimated loss, extrapolated by the trellis code's measured error — roughly 4× per added bit — and solved as a knapsack. The cheap alternative fails measurably: the curvature-weighted cosine between 3-bit and 2-, 4-, 5-bit errors is 0.035, 0.009 and 0.004, so errors at different widths barely align and the score never sees the destination width. The 248,320-row output layer is split into 16 bands by token probability, each held at 2 bits or more (a 12.1% KL reduction), and scales are tuned 70:30 on coding and general replies — coding-only tuning had left the 2.0-bit file at 77.3% on AIME; adding general data took it to 86.0%.

## Why It Matters

Bits per weight was always a proxy, tuned against perplexity and short benchmarks. The customer is now an agent emitting a hundred thousand tokens across a hundred and fifty tool calls, where one bad edit at step nine poisons steps ten through eighty. Perplexity cannot see that; neither can AIME.

Three of NariQ's four changes choose what to measure — which tokens enter the loss, which modules get bits, which tokens deserve precision — and none touches the codebook. Quantizer research has spent a year shaving the last percent off vector codes while the target drifted from "predict web text" to "finish a multi-hour task". The KL table shows the blind spot: at 2.5 bpw NariQ measures 0.0117 agent KL, beating Unsloth's 2.85 bpw file at 0.0275 by more than 2×, with both unremarkable on math.

There is a measurement warning too. NariQ's runs use Claude Code; Unsloth's published numbers use mini-swe-agent. Same model, same 113 tasks, and BF16 solves 44 in the first harness against 45.7 in the second. Absolute scores are not comparable across the table; only the percentage of each harness's own BF16 is. The authors answer the objection directly — their BF16 baseline solves 45.0 tasks per round against Unsloth's 45.65 — but "matches full precision" is now a claim about a harness as much as about a file.

## Impact

10.1 GB of text weights plus a 0.9 GB projector fits a 24 GB GPU with about 135K tokens of context; the full 262K needs roughly 30 GB, because the KV cache is still BF16. Decode runs 89 tokens/s at 4K and 71 at 64K on one RTX PRO 6000 — slower than a larger 10.9 GB IQ3_XXS file at 94.7, so the gain is capability per byte, not throughput.

The limits are the useful part. At 2.0 bits the file solves 33 DeepSWE tasks and drops 11.7 points on AIME: below about 2.5 bits the agent breaks before the benchmark does. The llama.cpp fork is tuned for one GPU generation (SM120); an M3 Pro decodes at 3.8 tokens/s, and pre-Ampere cards fall back to CPU.

For teams running local agents: use 2.5–3.0 bpw for tool-heavy work, never 2.0; validate any quantized file on your own harness with your own tools; and expect the next cliff at the KV cache.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [NariQ model card](https://huggingface.co/nari-labs/Qwen3.8-27B-NariQ-GGUF) & [paper (arXiv 2611.26157)](https://arxiv.org/abs/2611.26157) | HN Discussion: [123 points, 25 comments](https://news.ycombinator.com/item?id=50611402)*
