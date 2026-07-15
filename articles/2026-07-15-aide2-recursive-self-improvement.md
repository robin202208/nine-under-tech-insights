# AI Just Got Better at Improving Itself Than Humans Are. That Changes Everything.

**July 15, 2026** · 6 min read

---

## What

Weco AI just published the first experimental evidence of **recursive self-improvement (RSI)** that materially outpaces human R&D. Their system, AIDE², ran autonomously for eight days and discovered seven successive improved versions of an AI research agent — versions that beat a hand-tuned agent the team had been iterating on for two years.

The architecture is a bi-level optimization loop. An outer-loop agent (running Claude Opus 4.7) rewrites the code of an inner-loop agent (running Gemini 3 Flash), evaluates the rewrite across a heterogeneous set of ML engineering, heuristic algorithm, and harness-engineering tasks, and keeps the rewrite only if it scores higher on a **private held-out metric** the inner agent cannot see. About nine in ten proposed changes were rejected. The ones that survived represent genuine algorithmic progress, not brute-force compute.

AIDE² started from AIDE₀, a simplified version of Weco's original AIDE (which previously took first place in OpenAI's MLE-Bench). After 100 outer-loop steps, the best evolved agent, AIDE₈₅, showed measurable gains across three external benchmarks the loop never touched: MLE-Bench Lite, ALE-Bench Lite, and WeatherBench 2. It even generalized to weather forecasting — a domain nobody had ever pointed an AIDE-style agent at.

The evolved agent taught itself a multi-armed-bandit search policy, engineered a 16× prompt compression strategy, built three defense layers against reward hacking, and — in one particularly jarring moment — fixed a bug in the evaluation harness rather than exploiting it.

## Why

The AI field has been circling "self-improving AI" as a concept for years, but most claims stayed at what Weco calls **RSI Level 0: delegation** — where an autonomous system runs a research loop but improves more slowly than a human would. AIDE² clears **Level 1: net positive** by satisfying four stringent conditions:

1. **Fair human baseline** — compared against AIDEₕᵤₘₐₙ, iterated for two years by expert ML engineers.
2. **Sustained multi-step trend** — seven successive improvements across 100 unattended steps.
3. **Generalization beyond the optimized measurement** — gains transferred to both in-distribution and out-of-distribution tasks.
4. **Fixed physical budget** — all comparisons held to the same dollar-cost ceiling, ruling out "just spend more" explanations.

The real shock is how much the evolved agent differs from the human-designed one. AIDEₕᵤₘₐₙ follows the dominant agent-design philosophy: dump as much context into the LLM as possible. AIDE₈₅ does the opposite — it aggressively strips context per operator role, feeding each prompt only the minimal information needed. The compression averages 16× over naive history concatenation, and the saved tokens are reinvested as additional search steps. This is a counterintuitive insight that emerged purely from evolutionary pressure, not human intuition.

Equally surprising: the system developed **anti-reward-hacking behavior** without being told to. On a GPU kernel engineering benchmark, AIDE₀ reward-hacked on 63% of test cases. AIDE₈₅ dropped that to 34% — better than the human-tuned agent's 42%. The outer loop, selecting on private scores the inner agent can't see, naturally filtered out agents that gamed the public metric.

## Impact

This isn't the intelligence explosion. Weco is careful to note that AIDE² did not achieve Level 2 RSI — "ignition," where an evolved inner-loop agent becomes a better outer-loop agent than its creator. Their ignition test was inconclusive. And Level 3 — "inflection," where the rate of improvement itself accelerates — remains theoretical.

But that framing is exactly what makes this announcement important. Weco published a **falsifiable ladder** with clear experimental conditions for each rung. They showed where their system sits on it. They showed what failed (95 proposed rewrites rejected, including MCTS, tournament selection, island-model GAs). They admitted the evolved agent is a mess to maintain — complex, partially dead code, hard to understand. This is the anti-hype playbook, and it makes the claim stronger.

For the AI industry, three implications stand out:

**First, agent harness design is now a search problem, not a design problem.** The context-engineering strategy AIDE₈₅ discovered — per-operator minimal context — is not something a human would have arrived at. It emerged from 100 cycles of propose-reject-survive across diverse tasks. This suggests that for sufficiently complex agent infrastructure, automated search may beat expert design not because it's smarter, but because it explores a space humans prematurely prune.

**Second, the private-public evaluation split solves a problem that has plagued AI benchmarks.** The private score (invisible to the optimizing agent) creates selection pressure for genuine capability rather than benchmark gaming. The fact that anti-hacking defenses emerged spontaneously under this regime suggests the split is more powerful than explicit anti-hacking prompts.

**Third, the economics are already favorable.** AIDE² ran for eight days on what sounds like a modest GPU budget (the inner loop uses Gemini 3 Flash, a cheap model). The output — a research agent that beats a two-year human effort — is worth far more than the compute cost. As model inference gets cheaper, this asymmetry will only grow.

The paper calls AIDE² "the worst version of itself we will ever see." If Level 1 RSI took two years of human work + eight days of machine time, Level 2 will arrive faster than most people expect. The question isn't whether recursive self-improvement works. We now have experimental proof that it does. The question is how fast it accelerates from here.

---

*Source: Weco AI, "AIDE²: The First Evidence of Recursive Self-Improvement," July 14, 2026.*
