# A Founder's Honest Accounting of Running a Business on Local AI

> Alex Ellis spent $12,000 on a GPU, hooked it up to his team's coding agents, and discovered that local models aren't just worse cloud models — they're a fundamentally different tool.

## What Happened

Alex Ellis, founder of OpenFaaS and a team of developers building infrastructure products (microVMs, CI runners, HTTP tunnels), published a remarkably candid accounting of running local AI models in production. No benchmarks, no cherry-picked demos — just receipts from a software business that processes real customer data.

The setup: an RTX 6000 Pro Blackwell ($12K at purchase, now $15K+) with 96GB of VRAM, running two independent llama.cpp instances serving Qwen 3.6 27B (Q8_K_XL quant) to the team via a custom opencode provider. MTP speculative decoding delivers 93% acceptance rates, pushing generation from 67 tok/s to 130–200 tok/s sustained. The card paid for itself within two months when the team discovered a customer had been under-reporting licenses and underpaying by 4–5× for over a year — revenue recovery that would have been impossible with a cloud model, because running customer telemetry through ChatGPT or Claude would violate contractual data obligations.

## Why It Matters

The article cuts through the hype that has dominated local AI discourse. Ellis directly challenges the claim that "Qwen is only 12% behind Opus on SWE-Bench." The reality: local models have catastrophic failure modes that benchmarks don't capture. The central problem is **looping** — the model gets stuck, repeats the same output over and over, burning 600W for 30 minutes, and cannot self-correct.

Ellis's metaphor is devastatingly accurate: using a local model for unsupervised agentic work is like tempering steel by eye — "if you go one shade past where you need, you have to start over." The model runs hot, shoots past the goal, and loops. Unlike Claude or Codex, which can work unattended for 15 minutes making real progress toward a complex goal, Qwen requires constant supervision. It hallucinates concurrency issues in code review, invents filenames during filesystem exploration, and will read every file on your machine when asked for a simple forensic report.

But the flip side is equally real. There are tasks where local models **excel precisely because they're not cloud models**: analyzing customer diagnostic dumps in air-gapped VMs, processing telemetry databases where privacy matters, and providing fast, bounded customer support without leaking data to a third party. These aren't second-class use cases — they're use cases that cloud models simply cannot serve.

## Developer Impact

For teams building with AI, the implications are clear and uncomfortable:

**Local models are operations-intensive.** Identity, access control, metering, quotas, model routing, power monitoring — "local AI turns into an operations problem." Ellis built Toilgate, a custom provider that lets team members select models from a managed catalog, because just handing out a llama.cpp URL doesn't scale beyond one person.

**The "near-SOTA" framing is misleading.** A 27B model scoring 77.2 on SWE-Bench vs Claude's 88.6 sounds close. In practice, Qwen cannot write Go all day, cannot be trusted unsupervised, and fails at basic arithmetic (counting 27.3K as 273,000). It's not a worse cloud model — it's a marking knife when you need a chisel.

**The best strategy is dual-wielding.** Ellis's team uses cloud models (Claude, Codex) for long-horizon development and local models for privacy-sensitive analysis, customer support, and well-bounded maintenance tasks. Match the tool to the job, not the benchmark to the marketing claim.

**Fine-tunes matter more than base models.** The team runs both stock Qwen and Qwopus (a community fine-tune with Chain-of-Thought traces), and switches based on the task. Temperature, quantization level, and context settings aren't optional — they're the difference between useful output and infinite loops.

## Our Take

Local AI crossed the usability threshold months ago, but crossing the **reliability** threshold is a different problem entirely. Ellis's experience validates what we've observed: the gap between "local models are good now" and "local models can replace your Claude subscription" is measured in trust, not tokens per second. For privacy-sensitive workloads, the calculus flips — and that's where local models earn their $15K GPU.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
