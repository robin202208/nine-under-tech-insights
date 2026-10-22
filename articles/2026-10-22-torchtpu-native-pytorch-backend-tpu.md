# PyTorch Finally Speaks TPU Natively — and It Cost More Than a Kernel Port

> Google's TorchTPU ships real ATen kernels and a `torch.compile` backend for TPUs. The interesting part is what Google had to invent to make eager PyTorch fit a functional compiler IR.

## What Happened

Google has open-sourced TorchTPU (`google-pytorch/torch_tpu`), a first-party PyTorch backend for Tensor Processing Units. Version 0.2.0 is on PyPI, it builds with Bazel, requires Python 3.11+, and the repository opened this March was still receiving commits the morning it reached Hacker News at 375 points and 89 comments.

Unlike PyTorch/XLA, where TPU tensors were lazy handles and graphs were assembled behind the user's back, TorchTPU registers ATen kernels for TPU. When an operation reaches the dispatcher, nothing runs on the accelerator: the runtime packages the op into a **deferred op** — a CPU-side object holding a StableHLO builder function plus the static parameters that form a cache key — and returns a real `at::Tensor` whose storage points at that node.

A background thread then analyzes the whole stream rather than one op at a time — how many executables to split the work into, which core it belongs on, which cached executable fits. The compilation cache has three tiers: process-local memory, machine-local shared memory (on by default for distributed jobs), and an optional remote cache such as Cloud Storage, keyed on graph structure, shapes, compiler options and runtime versions. `torch.compile(model, backend="tpu")` hooks in as a TorchDynamo backend that emits one StableHLO program per FX graph — skipping TorchInductor and reusing XLA.

Distributed support runs through a `c10d` ProcessGroup backend named `tpu_dist`. There is no NCCL analogue: collectives are lowered into `stablehlo.all_reduce`, `all_gather`, `reduce_scatter` and `all_to_all`, then compiled into the same TPU executable. That choice propagates outward — one process per device, slice topology declared *before* PjRt initializes, and every rank publishing the global `replica_groups`, because the collective needs the full membership map even for subgroups it does not belong to.

Validated coverage: TPU v5e through v7, Qwen, Llama, Gemma and Muse Glimmer, FLUX and WAN diffusion, FP8/FP4, multi-slice training to roughly 8,000 chips, and vLLM, SGLang, TorchTitan and Ray integrations. Production inference is multi-host with prefill/decode disaggregation.

## Why It Matters

Three problems had to be solved, which explains why earlier ports stalled.

**Static single assignment.** StableHLO cannot mutate a tensor in place; every write conceptually yields a new value. PyTorch code is saturated with views, in-place `add_` and aliasing assumptions. TorchTPU reconstructs view semantics on top of an SSA IR via what the docs call *view decomposition and inversion* — the load-bearing trick, and why a naive graph exporter cannot substitute for a backend.

**Static shapes.** XLA wants shapes known at compile time. TorchTPU relaxes this with dynamic ops that trigger recompilation, but `SymInts` and jagged tensors remain experimental, so variable-length serving still pays a graph-break tax.

**Launch overhead versus recompiles.** Pure graph mode recompiles on every new shape; pure eager mode pays a launch per op. Defer-and-fuse plus a tiered cache is the negotiated middle, with `torch.tpu.EagerMode` exposing escape hatches when determinism matters more than throughput.

Strategically, the CUDA moat has always been less about silicon than about the dispatcher. Every non-NVIDIA accelerator must answer "does my `torch.nn.Module` run?" — usually by maintaining a fork, as with `torch-neuronx` for Trainium, or by reimplementing the ATen surface. Google is conceding that PyTorch, not JAX, is where the models live, then competing at the layer it controls: compiler and interconnect. TPU 8i pairs 288 GB of HBM at 8.6 TB/s per chip with 19.2 TB/s of inter-chip bandwidth — numbers that only matter if frameworks can saturate them.

## Impact

For teams already writing PyTorch, migration becomes "swap the backend." LLM serving through vLLM or SGLang is the shortest path, both listed as validated. The friction is distributed setup, not model code: topology env vars before initialization, one process per device, and a different mental model for subgroups.

Read the feature table: FSDPv2 and MPMD are marked low performance, Helion kernels are experimental, and GLM, Kimi, DeepSeek, GPT-OSS, Mamba and DLRM sit in an experimental "hill climb" tier. With 279 open issues at 585 stars, this is an early project carrying production claims.

Two caveats. The license resolves as `NOASSERTION`, not a recognized SPDX identifier, so legal review is warranted. And TPUs are still not retail hardware: capacity comes through Google Cloud and select partners, which makes price-performance against NVIDIA parts — not framework elegance — the deciding variable.

What TorchTPU proves is that the ATen backend is now a portable contract. Tenstorrent's community `libtt` and AWS's Trainium stack are variations on the same shape. The framework war is over; the compiler war is where accelerators now differentiate.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [google-pytorch/torch_tpu](https://github.com/google-pytorch/torch_tpu) | HN Discussion: [375 points, 89 comments](https://news.ycombinator.com/item?id=50169053)*
