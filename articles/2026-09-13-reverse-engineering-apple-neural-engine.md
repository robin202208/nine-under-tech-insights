# Reverse-Engineering Apple's Neural Engine: The Dataflow Bet That Lost to the GPU

> A three-year-old abandoned Linux driver project, finally finished, exposes exactly which assumptions Apple committed to silicon in 2017 — and why transformers broke them.

## What Happened

Eileen Yoon has finally finished reverse-engineering the Apple Neural Engine (ANE) on the M1 — a project she shelved three years ago after concluding the block was "just not that useful." Her write-up maps the full internal machine: compute, datapath, scheduler, memory, and execution model, reconstructed from her out-of-tree Linux driver, a decompiled ARM64 firmware, and probes of compiled CoreML programs.

The compute array is the least surprising part: 16 cores, each with 128 FP16 (or 256 INT8) parallel multiply-accumulate lanes — 2,048 MAC lanes total. Within each lane, a 16-bit multiplier feeds a 32-bit adder whose local accumulator holds the running sum across cycles, accumulated as Q16.16 and read out as FP16. Probing that register with a dot product against a vector of ones shows the accumulator saturates at 2^15, not at the FP16 boundary — the clamp happens inside the integer accumulator.

The revealing findings are in the control path. The ANE has no instruction set. A "task descriptor" is a serialized register-file dump: a ControlDMA engine burst-writes 32-bit configuration words into the datapath's MMIO register blocks (`0xf401f800` for the kernel path decodes as "copy 62 words into register base 0x1f800"). The driver never sees a CONV or MATMUL opcode — it stages a pointer and rings a doorbell (`TM_PUSH`). Eight task queues, each with a 32-entry base-address relocation table, stand in for GPU hardware channels.

Memory is where Apple's 2017 assumptions are frozen hardest. There are 16 private 64 KiB kernel-memory banks (1 MiB total, replicated once per core) plus a single shared 2 MiB L2. Three DMA engines move data: sixteen `KernelDMASrc` lanes for weights, and `TileDMASrc`/`TileDMADst` for activations. Kernel DMA is load-only, and there is no L2-to-KMem path at all.

## Why It Matters

Yoon's framing is that ANE's specialization was never the MAC — a dot product is a dot product — but the dataflow wrapped around it: when and where operands enter, stay, and move. Apple optimized for dense CNN inference, where weights are written once and reused across an entire output. That asymmetry is why kernel memory is private, replicated 16×, and load-only. For a convolution, that is a no-brainer.

Autoregressive transformer decode inverts the assumption. Generating one token requires streaming a whole model's worth of weights from DRAM. Weight reuse — the exact property the ANE was built to exploit — becomes the worst case rather than the common case. The dedicated kernel path stops being an optimization and becomes the bottleneck.

The roofline makes it concrete. The M1 ANE is rated 11 TOPS against 68 GB/s of system DRAM bandwidth. Because each MAC consumes two FP16 operands (4 bytes) for two operations, sustaining peak compute from DRAM would demand 22 TB/s — over 300× the available bandwidth. The ridge point is 162 operations per byte: below that arithmetic intensity, adding compute buys nothing.

Yoon then measures real DRAM read bandwidth: 37.99 GB/s through the ANE's kernel DMA, 59.08 GB/s through tile DMA, and 77.70 GB/s for the GPU via a Metal shader. Worse, combined kernel-plus-tile runtime matches the sum of the isolated runs (T_AB = 0.001 + 0.939·T_A + 0.981·T_B), evidence that the two DMA paths issue requests serially instead of overlapping. The ANE loses twice: each path is slower than the GPU's, and they never run concurrently.

## Impact

For hardware teams, accelerator differentiation lives in the dataflow and memory hierarchy, not in TOPS. The 162:1 ridge point is a usable filter — if a workload cannot supply that much reuse per byte fetched, a dedicated NPU will not beat the GPU it shares DRAM with.

For developers, "NPU TOPS" is a marketing number. What governs decode speed is who can stream weights faster and more concurrently, which is why Apple folded the ANE cores into the GPU cores on the M5 (2025) and led with "LLM performance." The standalone NPU's apparent demise is really a dataflow correction.

And for anyone who assumes vendor documentation is enough: none of this is in a datasheet. It comes from hexdiffs of compiled CoreML programs, a decompiled firmware debug routine that dumps the kernel-memory banks and L2, and an impulse-response test — a single 1 planted in a 33-entry lookup table — that proves the activation unit is a piecewise-linear interpolator with knot spacing 2^-R. Microarchitecture still has to be excavated, one register at a time.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Retrospectively Reverse-Engineering Apple's Neural Engine](https://eiln.github.io/posts/ane.html) (Eileen Yoon) | HN Discussion: [221 points, 31 comments](https://news.ycombinator.com/item?id=49670032)*
