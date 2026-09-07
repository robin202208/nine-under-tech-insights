# Frontier LLMs Drive Real Robot Arms: 95% Success on Pick-and-Place, Then Stall on Precision

> Robocurve put GPT-6 Astra, Claude Fable 5, and Fable 5.1 behind the same real robot arms and published 120 fully recorded trials — the gap between frontier models is enormous on easy tasks, and vanishes into a shared wall on hard ones.

## What Happened

Robocurve, the independent evaluation lab behind the earlier Claude Fable robot-arm comparison, gave OpenAI's GPT-6 Astra control of the same bimanual I2RT YAM arms under the same open-source [Inspect Robots](https://github.com/robocurve/inspect-robots) agent harness, on the same two tasks: placing a red block into a bowl, and inserting a round puzzle piece into a matching circular groove. No robotics fine-tuning, no specialized VLA policy — just an agent policy that sees three camera views plus proprioceptive state each turn and emits absolute end-effector poses via a `move_to` tool, with a 20-LLM-call budget and default safety guardrails. Every one of the 120 trials (20 per model per task) is public with transcripts, videos, and raw run data.

The headline result is dramatic. On the bowl task, Astra completed **19 of 20** trials (95%), against Fable 5.1's 8 of 20 (40%) and Fable 5's 1 of 20 (5%). It was also roughly three times faster (2.5 vs 6.8 minutes per run) and dramatically cheaper: an estimated **$0.94 per run vs $2.12** for Fable 5.1 and $2.69 for Fable 5, driven by a 6-9x reduction in output tokens (2.1k vs 12.9k vs 19.2k). Human graders scored each run on a five-stage rubric (no approach → contact → lift → position → placed), so failures still record how far a model got.

## Why It Matters

The bowl-task spread — 95% / 40% / 5% — is one of the clearest cross-model capability gaps measured on physical hardware, and it correlates with reasoning economy: Astra thinks less (2.1k tokens/run) yet succeeds far more often. That inverts the intuition that more deliberation buys more dexterity, and it suggests that for embodied control, the binding constraint is not compute spent but perception-to-action competence per token.

The puzzle task tells a different, arguably more important story. All three models stalled at the same final step: Astra completed the insertion only 2 of 20 times, identical to Fable 5.1, with Fable 5 at zero. When the task requires precision placement rather than gross manipulation, the frontier advantage collapses to a shared plateau. This is evidence that today's LLM-driven manipulation bottleneck is not model-specific reasoning but a common failure at fine, contact-rich final steps — the same "last centimeter" problem that separates demos from manufacturing.

The study design also matters. Human rubric grading instead of binary success, per-stage partial credit, disclosed limitations (trials not interleaved, bowl runs on different rigs, operator-known grading, list-price costs with Astra's automatic caching understated), and full trial artifacts make this one of the more honest hardware evaluations published this year — a contrast to the curated highlight-reel demos that dominate model launches.

## Impact

For robotics engineers, this is a usable lower bound on what a frontier LLM can do as a drop-in arm controller with zero training: reliable gross pick-and-place at under a dollar and a few minutes per attempt, but not yet precise insertion. That puts a price on the remaining gap and gives VLA and RL pipelines a concrete benchmark to beat.

For the evaluation community, the methodological template — open harness, staged human rubric, published per-trial data, cost and token accounting — is worth copying. When 95%-vs-5% spreads coexist with identical 10% scores on a harder task, single-number leaderboards are clearly misleading; task-difficulty stratification is the only honest way to compare embodied agents.

For teams building on top of these models, the economics are the sleeper finding: token-efficient action selection, not raw accuracy, is where the next generation of frontier models differentiates in the physical world. Anyone planning robot fleets or teleoperation products should benchmark per-attempt cost, not just success rate.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Robocurve — GPT-6 Astra on robot arms](https://openai.robocurve.org/gpt-6-astra/) | HN Discussion: [231 points, 180 comments](https://news.ycombinator.com/item?id=49582582)*
