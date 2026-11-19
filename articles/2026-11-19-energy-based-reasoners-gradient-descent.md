# 1.8 Million Parameters, 345× Less Compute: Reasoning as Gradient Descent Down a Learned Energy Landscape

> A Sudoku-Extreme solver roughly 50× smaller than its baselines, finishing in a fraction of the tokens — because it can tell, numerically, when to stop improving.

## What Happened

A paper released this week, *Energy-Based Reasoners: Your Recurrent Reasoner Should Be an Energy-Based Model* (arXiv:2611.08977, William Chen and Zeyu Li at Duke, Alexi Gladstone and Heng Ji at UIUC, Yilun Du at Harvard), attacks a structural weakness in the looped-model family — the HRM/TRM lineage of small recurrent networks that solve puzzles by iterating a shared block in latent space instead of emitting tokens.

Looped reasoners scale effective depth by looping, but their updates carry no explicit measure of progress — nothing says whether the current latent state improved, or when to stop. Existing systems compensate with a fixed iteration count and, at test time, by drawing many samples and re-ranking them with an external verifier or majority vote.

EBRs replace that with one learned object: a scalar energy `E_θ(z; x)` over a latent reasoning state `z` given input `x`. The loop becomes projected gradient descent, `z_{t+1} = Π_M(z_t − α∇_z E_θ(z_t; x))`, where `Π_M` keeps the state on the valid manifold — for Sudoku, never overwriting a clue, never repeating a digit in a row. That scalar then does four jobs: it **scores** a candidate, its **gradient** gives the refinement direction, its **norm** (`‖∇E‖ < τ`) is the stopping rule, and `argmin` across trajectories **selects** the answer. No external verifier enters the loop. Training adds exploration-based sampling so the landscape covers states the model would not visit on its own — which is what lets one model hold many distinct solutions instead of collapsing onto one.

The numbers are the argument. With **1.8M parameters**, EBR reaches 99.9% on Sudoku-Extreme using five samples — ahead of EqR's 99.8% at 128 samples and 5–7M parameters, for **345× fewer inference FLOPs**. Adaptive stopping alone saves 10.9× the compute a fixed-depth version spends. On N-Queens it hits 100% pass@1 at 8×8 and 99.5% at 10×10; on graph coloring it produces zero edge conflicts. Multi-solution coverage — the share of all valid solutions found in 20 samples — reaches 98.1% and 92.0% on N-Queens, against 89.7% and 57.5% for the strongest prior model.

## Why It Matters

Reasoning inference pays two cost centers: sampling N candidates because any one may be wrong, then verifying them with a reward model, a test suite, or an LLM judge. That second stage is why test-time compute scales badly — the verifier is often as large as the model it grades.

An energy function collapses the two: sampling, ranking, and stopping all read from the same scalar, at the cost of one forward pass. Nothing separate has to judge the output, and no token budget has to be guessed in advance. Compute tracks difficulty — easy boards stop after a few descent steps as the gradient dies, hard ones keep stepping.

The ratio matters more than the headline multiplier: the workload is not one puzzle but millions of structured calls — schedule feasibility, configuration synthesis, placement checks, constraint validation inside an agent loop. At 1.8M parameters, it fits alongside almost nothing in memory and runs cheap enough that per-call cost stops binding.

The caveats are real. This is constraint satisfaction with an a priori rule set, not open-ended reasoning. The latent state is problem-shaped — an 81-cell digit grid, or a weight matrix over all 3,240 cell pairs for pairwise moves — and `Π_M` is hand-built from the rules. Commenters noted it is unclear what the projection becomes for free-form language, and that energy-based models are notoriously hard to train; here the projection is doing real stabilization work. Read the result as a strong claim about structured search, not a replacement for a language model.

## Impact

The transferable pattern is an energy head bolted onto an existing loop: keep the recurrent block, add a scalar critic, get calibrated stopping and cheap re-ranking for free. Teams paying a judge model to sort 128 samples should expect that step to move inside the model.

First adopters are domains where a projection already exists in code — type checkers, SQL schema validators, test suites, design-rule checks, scheduling with hard constraints. Wherever a candidate can be projected back to validity, energy descent applies.

Three things to watch: whether the approach survives without a hand-built projection, whether energy stays calibrated off-distribution (a miscalibrated energy is a wrong stopping rule, not just a wrong ranking), and whether exploration training preserves multiple modes as state spaces grow. For now the takeaway is narrower: for structured problems, verification need not be a second model — it can be a scalar the first model already computes.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Energy-Based Reasoners (arXiv:2611.08977)](https://energy-based-reasoners.github.io/) | HN Discussion: [300 points, 52 comments](https://news.ycombinator.com/item?id=50543989)*
