# An LLM Read 131,072 Weights and Unstuck a Model That Could Not Do Math

> MIT CSAIL researchers asked a frontier model to read and edit neural network weights directly — no gradients, no training loop. It worked, and the failure case is the most interesting part.

## What Happened

On November 2, Rikiya Takehi, Jayden Teoh, Alex Zhang, and Omar Khattab (MIT CSAIL) published early results for what they call **Weight-Space Programming**: a general-purpose LLM reading and writing a network's weights from a natural-language spec, without an optimizer or gradient descent. All prompts, traces, generated code, and evaluations are public.

The reading experiments are adversarial. Four PPO policies with identical shapes (a 500→32→32→5 MLP, 17,253 weights) were trained on four different grid games; given only the numbers as text, Astra named all four correctly. Three DDPMs (23,824 weights) for circles, squares, and triangles were identified from weights alone; tellingly, Astra drew the shapes as *outlines* while the networks generate filled ones — evidence it was reading output-layer geometry rather than running a forward pass. For three character-level language models, they stripped the input and output layers, leaving four anonymous transformer blocks: Astra still named the training language, and its list of copying (induction) heads matched the measured ground truth exactly in all three.

Writing is stranger. A PPO car policy stuck in a local minimum (0 successes in 256 episodes, stalling on a ramp) was fixed in one shot by editing 3.5% of its weights: 1,024 of 1,024 episodes. For three MinAtar games, Astra wrote networks from scratch — all weights starting at zero, no data, no loss, no rollouts — and beat PPO trained for 100M steps in all three. Against 500M-step PPO it scored 517.4 versus 25.3 on Breakout and reached 60% on Seaquest. A 1.48M-weight Go network was produced as 336 lines of Python, beating a same-size PPO opponent once allowed 50 forward passes.

Then the headline number. Qwen3.5-0.8B is prone to reasoning loops; 85 of its first 96 practice answers ran to the 32,768-token limit. Astra traced the looping to a single direction written by the MLPs of layers 15 and 19, and subtracted it from 64 output columns in each — 131,072 weights, 0.016% of the model. MATH-500 rose from 7.9% to 48.1%, GSM8K from 13.8% to 67.1%, IFEval from 21.0% to 62.3%, and answers hitting the token limit fell from 93% to 33%.

The control experiment cuts against the hype. Gemma 3 270M, which rarely loops (8.5% of answers hit its limit), could not be improved: every edit moved MATH-500 by at most about a point. Astra found ordinary arithmetic and algebra errors with no single component behind them.

## Why It Matters

Reasoning-driven optimization was confined to text artifacts — prompts, harnesses, training scripts — because humans can hand-edit code but not weights. Those weights were left to symbolic optimizers like Adam or Muon. This work argues the boundary is dissolving.

But reading the traces shows two different capabilities being conflated. In the game experiments, Astra is doing program synthesis and then compiling the program into matrices: for Breakout it programs the first layer to compute where the ball will land and the second to move the paddle there. That is impressive compilation, not introspection. The Qwen result is the genuinely new one: a rank-one intervention that interpretability has used for years, but located by reasoning about failure modes rather than a hand-built probe suite.

Hacker News reacted with immediate skepticism about provenance: the top objections argue this is likely deliberate training on weight dumps aimed at recursive self-improvement rather than emergence, citing prior work on parameter prediction. That debate is unresolved, and the authors do not claim otherwise. What is not in dispute is the Gemma null result: weight editing removed an obstacle that was hiding an ability; it did not add an ability the model lacked.

## Impact

Three consequences follow. First, **auditing without evaluation**: if weights can be read, backdoors and hidden traits might be found without a test set designed to elicit them — though the sycophancy experiment warns that weights alone failed to separate an honest model from a sycophant until attention patterns were exposed. Second, **cheap, surgical steering**: touching 0.016% of parameters avoids the catastrophic forgetting that full fine-tunes cause, which makes unlearning a different economic problem.

Third, and least discussed: a supply-chain problem. A 131,072-weight edit is invisible to file-size checks, checksums, and weight-norm monitoring, yet it can install or remove behavior. Model artifacts are becoming patchable in a way our provenance tooling does not model.

The authors are building a benchmark next. Until it exists, treat this as a capability demonstration with an unusually honest negative result — and start asking what a signed weight diff would even look like.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Sparks of Weight-Space Programming (MIT CSAIL)](https://rikiyat.github.io/blog/weight-space-programming/) | HN Discussion: [1,917 points, 607 comments](https://news.ycombinator.com/item?id=50341174)*
