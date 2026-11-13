# Every Checkpoint Remembers the Chip That Made It

> By recovering a pseudorandom seed and a GPU's thread count from published model weights, a forensic lab says six Chinese frontier models were initialized on export-controlled Blackwell hardware — using nothing but the files the labs released themselves.

## What Happened

On November 12, Unemployment Labs published a report by Will DePue that turns an overlooked artifact of PyTorch into an audit trail. A model's weights do not start at zero. They are filled with pseudorandom numbers by Philox, a deterministic generator seeded with a single integer. PyTorch hands that job to cuRAND, which splits the sequence across every thread on the GPU. For a large enough initialization, NVIDIA hardware uses 2,048 threads per streaming multiprocessor — so the SM count decides how the numbers are dealt out. Hopper's H100 SXM, H200 and H800 have 132 SMs (270,336 threads), B200/B300 have 148 (303,104), GB200/GB300 have 152 (311,296). The same seed on a different chip scatters the same values into different slots.

That layout leaves two kinds of trace. Weights for tokens never seen in training stay bit-identical to their starting values. Weights that training did move retain a faint ordering — across 745,472 weights per layer in DeepSeek-V4-Flash, r = 0.066 at 57 sigma under the Blackwell layout, and no relationship under Hopper. After searching roughly six trillion seed-and-layout combinations and testing the winner on held-out weights, six models match Blackwell layouts: DeepSeek-V4-Flash and MiniMax-M3 at 148 SMs; DeepSeek-V4-Pro, Kimi K3, Kimi K3.1-Pro and Ant Group's Ling-3.1-flash at 152. Their predecessors — DeepSeek-V3, Kimi K2.5, MiniMax-M2.7, Ling-3.0-flash — read as Hopper.

The verification is what makes this more than a statistical story. The authors recovered DeepSeek's seed (953049583) and reproduced 445 entire rows of V4-Flash bit for bit on a physical B300; the same seed on an H100 reproduced DeepSeek-V3's preserved values exactly and V4-Flash's not at all. Kimi K3 was reproduced on a rented GB200. For 24 models where the developer publicly named its hardware, the method agrees every time. Strongest evidence per model runs from 49 to 1,385 sigma, against a chance threshold of about 8 sigma for a search that size. One alternative survives the layout test — an MI300X partitioned into 152 units — so they compared the values themselves: where AMD and NVIDIA arithmetic diverges, V4-Flash matched NVIDIA 363 times and AMD zero.

## Why It Matters

Every published checkpoint is now a statement about the compute that produced it. Export controls have always been enforced against manifests, shell companies and seized hardware. This moves the evidence inside the artifact: a lab can deny a cluster, but cannot retract the initial values it already uploaded. DePue's team is careful that this supports rather than proves training on the same chips — weights can be initialized on one system and trained on another, and chips can be rented outside China legally. What the report establishes is access, not smuggling.

The statistical discipline is the template. Six trillion candidates guarantees lucky hits, so they set an eight-sigma threshold, picked combinations on one set of weights, and validated on another. The physical re-runs on real H100, B300 and MI300X close the loop. Compare that with the usual benchmark claim: one number, one run, no held-out set.

The limits are just as instructive. PaddlePaddle, which trained Baidu's ERNIE-5.5, leaves no such pattern; Qwen3 models initialized on CPU leave no GPU fingerprint; closed weights cannot be inspected. Provenance forensics works exactly where a lab is transparent enough to be checked — which is where it matters.

The arithmetic spills in both directions. From model size, token counts and release timelines, the report estimates the five flagged runs at roughly 12,000 Blackwell GPUs, with the two largest at about 3,000 and 4,500 chips — $200–300 million for a single cluster at implied market prices. The authors flag these as assumptions, but the shape is unavoidable: publishing weights publishes your cluster size.

## Impact

For labs, initialization is now a disclosure channel. Any team that publishes weights reveals the SM count of whichever machine filled the first tensor, and with it a rough generation and vendor. Initializing on CPU, or under a framework with different random-number plumbing, would erase the signal — and look like evasion.

For regulators, this is a first artifact-level evidence standard in the export-control debate, with built-in caveats: hardware access is not hardware location, and a rented offshore cluster is legal. Enforcement still has to prove a shipment.

The durable lesson: weights carry history beyond capability. A checkpoint records how it was created, in a way no telemetry and no license agreement does — and that record survives quantization, fine-tuning and re-upload, as long as someone keeps the seed.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Unemployment Labs — Checkpoint Forensics](https://unemploymentlabs.com/checkpoint-forensics) | HN Discussion: [302 points, 110 comments](https://news.ycombinator.com/item?id=50474883)*
