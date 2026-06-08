# Anthropic Just Confirmed AI Is Building AI — And It's Accelerating Faster Than Anyone Expected

> Anthropic's landmark report reveals that Claude now writes 80% of Anthropic's production code, ships 8× more output per engineer, and is approaching the capability to autonomously design its own successors. The company is calling for a global pause.

## What Happened

On June 5, 2026, Anthropic's research arm published [*When AI builds itself*](https://www.anthropic.com/institute/recursive-self-improvement), a data-driven report documenting how AI systems are now the primary drivers of AI development inside the company. The paper provides internal metrics never before disclosed publicly — and the numbers are sobering.

The headline stat: **more than 80% of code merged into Anthropic's codebase in May 2026 was authored by Claude**. As recently as February 2025 (pre-Claude Code), that number was in the low single digits. The average Anthropic engineer now merges **8× as many lines of code per day** as they did in 2024 — not because they're writing more, but because Claude handles the execution while humans shift to direction-setting and review.

The pattern extends beyond engineering. In a controlled experiment, Claude Mythos Preview achieved a **~52× speedup** on code optimization tasks compared to baseline, up from ~3× a year earlier. For calibration: a skilled human researcher needs four to eight hours to hit 4× on the same task. Claude is now **superhuman** at experimental optimization within well-defined boundaries.

## Why It Matters

The core argument of the piece is that AI development is approaching **recursive self-improvement** — the point where AI systems can autonomously design and develop their own successors. The report breaks this into a spectrum of increasing capability:

1. **Code generation** (2023-2025): Humans write specs, AI generates snippets.
2. **Coding agents** (2025-2026): AI writes and edits entire files autonomously.
3. **Autonomous agents** (today): AI delegates hours of work to other agents, runs code, and ships fixes independently.
4. **Closing the loop** (20XX?): AI designs new models, runs training, and produces successors — without human intervention at any step.

The report provides evidence that step 4 is approaching faster than institutions expect. Claude's success rate on fully open-ended tasks — the kind with no clear specification — reached **76% in May 2026**, up 50 percentage points in six months. When researchers deliberately took wrong turns in investigative sessions, Claude Mythos Preview suggested a better next step than the human **64% of the time**.

The METR benchmark (which measures how long AI systems can work reliably without human intervention) tells the same story: task horizons have been doubling roughly every **four months**, accelerating from an earlier seven-month doubling rate. Claude Opus 4.6 can now handle **12-hour tasks** reliably. If the trend holds, AI systems could handle week-long tasks by 2027.

## Developer Impact

The most provocative finding is the **automated researcher** experiment. Anthropic gave Claude-powered agents an open problem in AI safety: can a weaker model reliably supervise a stronger one? Two human researchers recovered 23% of the gap over about a week. The AI agents recovered **97%** over 800 cumulative hours, using ~$18,000 in compute — and designed every experiment themselves. Humans only set the problem and the scoring rubric.

This has immediate implications for anyone building AI systems:

```python
# The bottleneck is shifting from "can we build it?" to "should we build it?"
# Anthropic's internal workflow now looks like:

class AICompany2026:
    def engineer_workflow(self, goal: str):
        # Human: "Fix the training crash" (direction only)
        # Claude: isolates bug, tests 100+ hypotheses, ships fix
        # Human: reviews, approves

    def researcher_workflow(self, problem: str):
        # Human: "Can a weak model supervise a strong one?"
        # Claude agents: propose hypotheses → run experiments →
        #   share findings with parallel agents → iterate
        # Human: evaluates results, picks next problem
```

The report's own admission is striking: "The doing — writing the code, running the experiment, producing the result — now costs almost nothing in human time." The human comparative advantage has narrowed to "research taste and judgment."

**Amdahl's Law** now governs AI development. As one part of the pipeline accelerates, the un-accelerated parts become the bottleneck. Anthropic reports that human code review is already struggling to keep pace with Claude's output, and the explosion of new ideas from AI-assisted research exceeds the organization's capacity to pursue them.

## Our Take

Anthropic is saying out loud what every frontier lab has been seeing internally: AI systems are now the primary force driving AI progress, and the feedback loop is tightening. The call for a verifiable global pause mechanism is the right instinct, but the technical challenge of building a verification regime that works on general-purpose compute — where training runs are easy to hide — is immense. The window for building coordination infrastructure is measured in months, not years, and the Amdahl's Law bottleneck for society isn't compute or model capability — it's institutional adaptation speed.

---
*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
