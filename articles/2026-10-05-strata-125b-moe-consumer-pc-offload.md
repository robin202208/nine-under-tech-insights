# A 125B Model on a 12 GB Gaming PC: Inside Strata's Three-Tier Offload Engine

> Strata runs Qwen3.8-Flash-Next — 125 billion parameters, 24,576 experts — on consumer hardware by treating VRAM, RAM and the SSD as one memory hierarchy, and letting the CPU and GPU work experts side by side.

## What Happened

A community project called Strata shipped an MIT-licensed inference engine that runs Qwen3.8-Flash-Next, a 125-billion-parameter model that activates 6B parameters per token, on an ordinary gaming PC. It needs 12 GB of VRAM, 32 GB of RAM and ~80 GB of disk; a 70 GB quantized download is the only weight transfer. A model this size normally needs servers with hundreds of gigabytes of graphics memory.

The model is a sparse mixture of experts: 512 experts per layer across 48 layers — 24,576 in total — of which 10 routed plus one shared expert fire per layer per token. On top sit a 51B N-gram embedding and a 4B multi-token-prediction (MTP) head. Strata fits it by splitting the model across three tiers:

- **VRAM** holds what every token needs — attention and Gated DeltaNet mixers, routers, shared experts, the output head — plus an adaptive cache of the few thousand experts a conversation actually uses, and the KV cache (past 64K, only its most-read part stays resident).
- **RAM** pins all 24,576 experts. The CPU computes the experts the GPU does not hold, in place, with AVX-512/AVX2 kernels, alongside the GPU.
- **SSD** holds the 28.8 GB n-gram table; only a few rows are read per token.

Two more tricks carry the speed. The model's own MTP layer drafts up to three tokens, verified in one sweep over all 48 layers — 2.4 to 3.2 accepted per pass. A prompt-lookup drafter adds up to five more where a reply quotes its context, worth 6–11% on code edits. Prompts are ingested in 8,192-token chunks, experts streaming to the GPU over PCIe during attention.

Measured on an RTX 5070 (12 GB), the compressed Q2_0 build generates 94 tokens/s and reads prompts at 2,650 tokens/s; the largest IQ3_S build falls to 53 tokens/s. Users report 124 tokens/s on a 24 GB RTX 4090.

## Why It Matters

The most interesting part is that Qwen designed the model to be offloaded. Its model card calls the N-gram Embedding a parameter-scaling axis "more amenable to offloading than Mixture-of-Experts" — the 20-million-entry bigram/trigram table consulted at layer 2, i.e. the 28.8 GB file Strata parks on the SSD. Labs are shaping architectures around the memory hierarchy of the machine that will *run* them, not just the cluster that trains them.

Second, Strata reframes the bottleneck. Decode is memory-bound, so bytes moved — not FLOPs — set the ceiling. Every extra gigabyte of VRAM holds roughly 700 more experts and removes their computation from the CPU, so a bigger card beats a faster one. It is the same roofline logic that decided the Apple Neural Engine and ternary-packing debates: on memory-starved silicon, where the weights live matters more than how fast they compute.

Third, the datacenter assumption keeps eroding. Training a 120B MoE on one node was a 2026 result; serving a 125B MoE from a gaming PC is its inference-side mirror. Clusters are not obsolete — but the frontier is no longer gated purely by their size.

The trade-off is quantization. To fit, the model runs at 2–3 bits, and commenters are openly skeptical of quality below 4 bits. One community benchmark put Strata's IQ3_XXS build at 114/128 (89.1%) on a code suite against 92/128 (71.9%) for a 27B model in Q4/Q5 — a comparison mixing model size and precision, suggestive rather than decisive.

## Impact

For developers, the practical result is a frontier-class coding model beside the editor with no per-token bill and no data leaving the machine — the argument once used to justify a $12,000 GPU, now at consumer-card prices. Strata speaks the OpenAI, Anthropic and Responses APIs, so existing agents point at `http://127.0.0.1:8080/v1` unchanged.

The caveats are real: cold start takes ~15 minutes to page the model in, and Apple silicon and older GPUs are community experiments rather than the happy path. One measured regression stands out: on a 50-image coordinate task, the same GGUF and vision adapter scored a median error of 154.8 pixels in Strata against 46.5 in llama.cpp — vision remains the least-tested path in local engines.

Strata is not the first attempt to run big models at home, but it is the clearest demonstration that a 125B mixture of experts can be a desktop application. The next milestone is quality: whether 2-bit experts plus a 125B base beat a 4-bit 30B one.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Strata (GitHub)](https://github.com/Niko1221/Strata) & [Qwen3.8-Flash-Next model card](https://huggingface.co/Qwen/Qwen3.8-Flash-Next) | HN Discussion: [612 points, 285 comments](https://news.ycombinator.com/item?id=49953495)*
