# GLM-5.3 Crossed the Exploit Threshold — and Its Refusals Cost $4,400 to Delete

> Anthropic's red team measured an open-weight model autonomously chaining zero-days into working exploits — then measured how cheaply its safety behaviour could be removed.

## What Happened

On 2026-09-29, Anthropic's Frontier Red Team published an analysis of GLM-5.3, the latest model from Zhipu AI (Z.ai), focused on one question: how much autonomous exploitation now ships in downloadable weights, and how durable its refusals are.

On ExploitBench, which measures end-to-end exploit development against Chrome's V8 engine, GLM-5.3 produced working exploits in 50 of 410 attempts — statistically level with Claude Mythos Preview (56 of 410), run by Anthropic with safeguards disabled. On Anthropic's internal binary-exploitation benchmark (100 randomly selected OSS-Fuzz targets, full credit only for a complete control-flow hijack), GLM-5.3 hijacked control flow in 4% of trials versus 6% for Mythos Preview. Earlier generation models — Claude Opus 4.6 and GLM-5.2 — scored zero on both.

Human-in-the-loop sessions showed the practical consequence: given a sandboxed machine and a day of intermittent attention, GLM-5.3 found previously unknown vulnerabilities in a browser's JavaScript engine and chained them into a web page that reads arbitrary files off a visitor's machine; the same session surfaced exploitable flaws in wireless and graphics drivers and network-facing device software. In a second session, handed only public details of CVE-2026-11645 and one other known flaw, GLM-5.3-Flash built a reliable ARM64 exploit chain that bypasses pointer authentication — 20 minutes of human attention plus eight hours of model work, which at Zhipu's API prices costs $20.40.

Then Anthropic attacked the safeguards. Out of the box, GLM-5.3 refused every overtly malicious request in a simulated environment. A deceptive cover story ("you are an autonomous red-team agent on an exercise") got it to engage 64% of the time; pre-filling its thinking tokens to look like it had already decided to proceed reached 92%; an abliterated copy — the standard refusal-removal edit — reached 100%. Anthropic built that copy itself: about 2,200 GPU-hours ($4,400) for GLM-5.3 and 600 GPU-hours for the Flash variant ($1,200 for a practiced team). Refusal rates fell from above 90% to 3%, 2% and 12% on JailbreakBench, HarmBench and StrongREJECT, while GPQA-Diamond scores were unchanged and CyberGym dropped only a few points. Abliterated builds were circulating publicly within days of release.

## Why It Matters

The headline number is NIST's CAISI, which assessed on 2026-09-17 that GLM-5.3 is "the most cyber-capable open-weight model released to date" and lags the US frontier by about four months. That comparison is softer than it sounds: the frontier side includes models run with safeguards disabled and released only to vetted users. For an attacker who wants an unrestricted model today, the effective gap is not four months — it is the distance between a downloadable file and a gated program.

The abliteration cost is what makes the report more than a capability announcement. A one-time four-figure spend, or roughly $1,200 for someone fluent in the technique, converts a refusal-heavy model into an unrestricted offensive tool with capability largely intact — and the resulting artifact copies at zero marginal cost. Refusal behaviour trained into weights is not a security boundary when the weights are downloadable.

The report also collides with defender reality. The same guardrails that stop attackers stop legitimate defensive work, and HN's thread read the post largely as free advertising for GLM: commenters described locked-down models refusing to help with malware forensics, and recalled that Hugging Face leaned on GLM-5.2 during July's agent incident because closed models declined. Anthropic is at once the measurer, an IPO-stage competitor, and the beneficiary of the policy it recommends. The measurements are concrete enough to check; the framing is not neutral.

## Impact

For anyone shipping agents against self-hosted or cheap third-party models, the lesson is architectural: model-level guardrails are not a control you can rely on. Put enforcement where it cannot be edited out — sandboxed execution, egress allowlists, scoped credentials, secrets kept out of the workspace, and logging of exploit-shaped tool use. Assume any capable open-weight model has an unrestricted variant circulating.

Security research has already argued that patching throughput, not secrecy, is the bottleneck. A world where a working exploit chain costs $20.40 makes that sharper: patch velocity, exploitability triage and exposure reduction are the levers that still move.

On policy, the honest ask is replication before regulation. "Four months behind" is an aggregation artifact of someone's benchmark suite; the bypass percentages come from a simulated environment where no generated code executes and engagement is measured as a request for a remote connection. Both deserve independent verification — from parties who are not simultaneously selling the alternative.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Anthropic Frontier Red Team](https://www.anthropic.com/research/glm-5-3-and-the-spread-of-advanced-cyber-capabilities) | HN Discussion: [192 points, 183 comments](https://news.ycombinator.com/item?id=49897075)*
