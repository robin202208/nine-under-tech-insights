# Kimi K3 Opens the 3-Trillion-Parameter Frontier

> Moonshot AI releases the first open-weight model at 2.8T parameters, matching or beating proprietary giants on key coding and agentic benchmarks.

## What Happened

On July 16, 2026, Moonshot AI unveiled Kimi K3, a 2.8-trillion-parameter Mixture-of-Experts model with native vision and a 1-million-token context window. It is the first open 3T-class model ever released, with full weights promised by July 27. The model is available immediately across Kimi's product suite — web, desktop, coding IDE, and API — at $3.00/MTok for cache-miss input and $15.00/MTok for output.

Architecturally, K3 introduces two novel components: Kimi Delta Attention (KDA) for efficient long-context attention scaling, and Attention Residuals (AttnRes) for selective cross-depth representation retrieval. Under the hood, it uses a Stable LatentMoE framework activating only 16 of 896 experts, achieving what the team describes as a "2.5x improvement in overall scaling efficiency" over its predecessor K2. The model was trained with quantization-aware techniques from the SFT stage onward, using MXFP4 weights with MXFP8 activations.

On benchmarks, K3 lands squarely between Claude Fable 5 and GPT 5.6 Sol — trailing them overall but decisively beating Claude Opus 4.8 and GPT 5.5 across the board. On Terminal Bench 2.1 it scores 88.3% (vs. Sol's 88.8% and Fable's 84.6%). On DeepSWE it reaches 67.5%, competitive with Fable's 70.0% and Sol's 73.0%. On agentic benchmarks like BrowseComp and DeepSearchQA, K3 leads or ties with both.

## Why It Matters

This release marks two shifts simultaneously. First, the raw parameter scale: no open model before K3 cracked one trillion parameters, let alone 2.8T. K3 is built on architectural scaffolding — KDA, AttnRes, Per-Head Muon, and Sigmoid Tanh Unit (SiTU) activations — explicitly designed to keep training stable and efficient at this scale. The technical report promises further detail, but the claim of a 2.5x scaling-efficiency gain over K2 suggests the architectural innovations are doing real work, not just marketing.

Second, and perhaps more significant, is the long-horizon agentic capability on display. The blog showcases K3 autonomously building MiniTriton — a from-scratch Triton-like GPU compiler with its own IR layer over MLIR, optimization passes, and PTX code generation — that matches or beats Triton on roofline benchmarks. In another case study, K3 designed a complete chip in a single 48-hour autonomous run using open-source EDA tools on the Nangate 45nm library, closing timing at 100 MHz and sustaining 8,700 tokens/s decode throughput in simulation. A chip designed by a model, for a model. These are not cherry-picked demo scripts — they are multi-hour, multi-tool autonomous engineering runs that exercise sustained reasoning and tool orchestration.

The model's self-reported limitations are also noteworthy: it is "excessively proactive," sometimes making decisions on the user's behalf when intent is ambiguous, and it is sensitive to thinking-history preservation across harness sessions. These are the kinds of rough edges you expect from a model trained aggressively on long-horizon autonomy.

## Impact

For developers, K3 changes the economics of frontier AI access. An open 3T model with weights available means fine-tuning, self-hosting, and customization at a scale previously reserved for API-only proprietary systems. The API pricing — $3/MTok input, $15/MTok output — is competitive with GPT 5.6 Sol and Claude Fable 5, but the open-weight path opens a fundamentally different deployment model for enterprises that cannot send data to third-party APIs.

For the ecosystem, K3 accelerates the open-weight arms race. With Moonshot joining DeepSeek, Qwen, and Meta in releasing near-frontier models openly, the gap between open and closed is shrinking to weeks. The HN discussion surfaced a recurring strategic question: are Chinese labs deliberately commoditizing AI intelligence to undermine the proprietary moats of OpenAI and Anthropic? Whether by strategy or circumstance, the effect is the same — the cost of frontier-capable inference is in free fall.

For the broader AI engineering community, the autonomous compiler and chip-design case studies signal something new. We are crossing a threshold where models can sustain coherent technical work over tens of hours, not tens of minutes. When a model can design a chip that runs another model, the recursive implications for hardware-software co-design are hard to overstate. Kimi K3 may trail Fable and Sol on raw benchmarks, but the practical demonstrations of long-horizon autonomy suggest we should be measuring capability differently.

---

*Published by [Nine Under Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Kimi K3 Blog](https://www.kimi.com/blog/kimi-k3) | HN Discussion: [1,134 points, 711 comments](https://news.ycombinator.com/item?id=48935342)*
