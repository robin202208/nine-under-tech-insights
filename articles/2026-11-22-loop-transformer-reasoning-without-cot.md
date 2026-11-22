# 48 Layer Applications From 24 Stored Layers: Reasoning Without the Written Chain

> Thinking Machines' Inkling 2 Mini reuses its middle layers three times per token, buying serial depth without reasoning tokens — and publishes the monitorability audit that architecture change makes unavoidable.

## What Happened

On November 20, Thinking Machines released **Inkling 2 Mini** with open weights: a 26.4B-parameter mixture-of-experts model with 3.4B active parameters and — the number that matters — **5.4B weight activations per token**, because the weights are not visited once.

The model is 24 layers arranged in four blocks of six. The prelude and final block run once; the two middle blocks run **three times**, so each token gets 48 layer applications from 24 stored layers. Layers 2 and 3 also read a "message" — the previous pass's output from those same blocks — merged with the prelude output through a learned gate that starts fully closed. Looping therefore enters training as a no-op and its gain ramps up, which is how the team added recurrent depth mid-flight without destabilizing the run. The design uses attention residuals (Kimi Team, 2026): each layer learns its own mix of earlier block outputs rather than accumulating a single running sum.

Position mattered. Looping the middle 12 layers beat looping the last 12 on 27 of 28 evaluations, with a compute-equivalent gain of 1.51–1.91× versus 1.24–1.36×. The bet is that early layers translate between embeddings and internal representations, late layers translate back, and abstraction lives in between.

Training was done in the deployment precision: expert layers ran NVFP4 from pretraining through RL, the final four layers MXFP8 — so no quantization conversion loss ships. Midtraining, two-thirds of the experts were pruned per layer (384 → 128, top-8 routing), then healed on 1T distilled tokens, followed by SFT and a 2.2T-token RL phase. Total footprint lands near 20GB. Reported scores: DeepSWE v1.1 48.2%, SWE-bench Verified 79.2%, Terminal Bench v2.1 71.6%, BrowseComp 79.0%, MCP Atlas 82.7% — ahead of every open-weights model under 100B total parameters on most of what it measured.

## Why It Matters

A standard transformer's serial depth is fixed at inference: one token, one pass through the stack. If a problem needs more sequential steps than the stack provides, the model must write them out — and that written chain of thought is what safety monitors read. Looping raises the depth ceiling without adding parameters, which means reasoning can happen where nobody can read it. That is the honest worry, and it is why the release is framed as a research artifact rather than a leaderboard entry.

The counterintuitive finding is that at this scale the worry did not materialize in the measurements they ran. On a variable-loop sibling trained with one to five passes, the no-CoT time horizon rose from 16 seconds at one pass to 37 seconds at three. Chains of thought *shrank* — 3,703 reasoning tokens at one pass versus 1,661 at five — yet legibility metrics barely moved: 8.7 words per sentence, 94.4% dictionary words, fewer backtracking markers. The model grew more direct, not more cryptic. On N-Hop Lookup, a clean test of chain-following without scratchpaper, accuracy kept improving out to ten loops even though training never exceeded five. CoT controllability stayed at or below 0.1%, against 1.3% for Qwen3.8-27B.

The other result is a clean separation of what depth buys. Knowledge-intensive scores — AA-Omniscience, SimpleQA Verified, VQA — are flat as loops increase. Looping buys multi-hop reasoning, long-context retrieval, and agentic endurance; it does not buy facts. That is why the model leans on search and tools, where it also leads its size class. And the compute trade is real but asymmetric: the non-looping version saved 7.5% of training compute yet needed 140% more inference compute to match loss on 512k-token agentic traces.

## Impact

For developers, the practical story is a 20GB model that runs offline on a 32GB laptop, and an independent compiler project already pushed it to 5.38GB at 1.65 bits per weight (88% GSM8K, 70.7% HumanEval) and to 8.87GB at 99.2% of the BF16 teacher — 60-plus tokens per second serial on an M5 Max. Local agentic coding crossed from demo to daily driver.

For safety engineering, the lesson is procedural: monitorability now depends on architecture, so it must be re-measured per architecture, not inherited from the last model. Loop count is a deployment parameter that changes behavior — the released variable-loop model still answered sensibly at double its trained depth, which is exactly the kind of extrapolation a monitor should not assume.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Thinking Machines — Introducing Inkling 2 Mini](https://thinkingmachines.ai/news/inkling-2-mini/) & [LookAtMyQuant results](https://github.com/ShinMegamiBoson/lookatmyquant) | HN Discussion: [1,385 points, 281 comments](https://news.ycombinator.com/item?id=50581260)*
