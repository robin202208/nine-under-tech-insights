# LLMs Don't Just Mirror Our Biases — They Invent New Ones

> Princeton researchers put frontier models in a 40-round hiring game with four made-up demographic groups. With no real differences between the groups, the models sorted them into job classes more aggressively than humans did — and the newer, more capable models did it worst.

## What Happened

A team from Princeton and the University of Chicago (Addison Wu, Ryan Liu, Xuechunzi Bai, and Thomas Griffiths) adapted an iterative hiring paradigm from social psychology and ran it on a broad set of LLMs. In the game, an agent acts as a hiring manager across dozens of rounds. Candidates belong to four *artificial* demographic groups — labels with no signal attached — and jobs fall into four quadrants along the warmth and competence axes (doctor, lawyer, childcare aide, janitor). Hiring a candidate who succeeds earns reward; the agent sees only the outcome.

The groups start equal. They do not stay equal. The models concentrate specific groups into specific job quadrants — behavior the authors quantify with three metrics: a stratification index (SI), between-group divergence (BGD), and a stochasticity index (GASI, whether the bias repeats across runs).

Human participants stratified too (SI = .84, BGD = .56). The models stratified *more*. Across the GPT, Claude, Gemini, Qwen, and Llama families, newer and larger versions segregated more than their predecessors — Claude 4 Sonnet's SI was more than eight times Claude 3 Haiku's under direct prompting. Higher scores on BBQ, a standard single-prompt bias benchmark, predicted *more* extreme segregation rather than less. And because each run reinforces whatever early successes it happened to observe, models learned a different bias every time (mean GASI of .52 versus .47 for humans); ablations traced that randomness to the binary success/failure of individual hires, not to sampling temperature.

## Why It Matters

The finding attacks the default assumption behind most fairness work: that bias is something already *in* the model — baked into pretraining data — and therefore removable by filtering outputs, debiasing embeddings, or fine-tuning on balanced data. That addresses representational bias. It does nothing for a bias the model invents during deployment, inside a stateful agent that carries beliefs across interactions.

The mechanism is an exploration-exploitation trade-off familiar from reinforcement learning. A strong model draws sharper inferences from past outcomes: if early hires from group A succeeded, the model rationally favors group A again. Exploit beats explore, so a handful of noisy early results hardens into an impression about an entire group. Counterintuitively, *better* inference makes this worse — more capable models make "more precise inferences about past outcomes," which starves exploration.

Most attempted fixes barely move the needle. Raising temperature did not significantly reduce stratification, and pushed chain-of-thought into gibberish at T = 1.5. Chain-of-thought alone helped only marginally. Lowering the base success rate to 0.1 — forcing exploration because exploitation rarely pays — cut stratification sharply (Claude 4 Sonnet's SI fell from 1.66 to 0.166), but substituting *realistic* per-job success rates (6–87%) erased most of that gain. Replacing the gamified hiring cover story with refugee resettlement or military conscription still produced stratification (average SI of 1.13 and 1.26). Remove the cover story entirely and the models collapse into a different failure: repeatedly picking whichever action succeeded first.

Only one intervention robustly worked — adding an explicit diversity term to the model's objective. Almost every model then allocated *more* equally than both the random baseline and humans. Simply instructing the model to be fair, or appealing to its internal values, mostly did not.

## Impact

For anyone shipping multi-turn agents that allocate resources — hiring tools, lending triage, support routing, or task assignment across a team — the paper is a warning that bias audit cannot be a one-time, pre-deployment check. Bias here is a property of the *trajectory*, not the weights. It compounds with each decision and is entangled with whatever persistent memory the agent maintains.

Two practical takeaways. First, if an agent will make repeated allocation decisions, treat "how much has it explored?" as a first-class design parameter and build in explicit exploration incentives rather than trusting a general-purpose reasoning model to stay even-handed on its own. Second, note the uncomfortable tension the authors name: the same pattern generalization that powers few-shot learning is what drives premature stratification. Suppressing bias by suppressing generalization would gut the model's usefulness, so the goal has to be selective — objectives that discourage harmful pattern-matching while leaving reasoning intact.

The models were not mirroring us. They were writing their own stereotypes, and the best ones wrote them fastest.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [arXiv 2511.06148 — Large Language Models Develop Novel Social Biases Through Adaptive Exploration](https://arxiv.org/abs/2511.06148) | HN Discussion: [198 points, 111 comments](https://news.ycombinator.com/item?id=49617581)*
