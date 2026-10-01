# Post-Training Leaves a Behavioral Shadow That Can Be Read One Word at a Time

> A private fine-tune can leak its new capability through single-token word choices on prompts that have nothing to do with the task — no weights, no logits, no target-task data required.

## What Happened

A technical report from Lovart Research and academic collaborators (arXiv:2609.29233, September 2026) argues that a model's post-training update does not stay inside the task it was trained on. It leaves a *behavioral shadow*: measurable changes in which ordinary word the model prefers on task-unrelated inputs. The paper introduces Active Taskless Distillation (ATD), a procedure that reads that shadow back into capability.

The setup is deliberately minimal. A teacher model is privately post-trained (say, on code). A student is initialized from the same public ancestor. ATD uses the ancestor to find prompts on which two ordinary single-token words are nearly tied in probability. Near such a tie, a small preference shift introduced by the private update can flip the chosen word. The teacher is queried for one greedy word per prompt, and each flipped tie becomes a one-bit observation of the update. The student then trains *only* on those prompt–word pairs.

On the primary code lineage with Qwen2.5-1.5B, 5,664 single-word teacher responses over 128 distinct words carried the student to 51.22% on HumanEval+ — matching the teacher's own aggregate coding score and beating an exact nuisance-matched control by **+5.34 points** (95% CI [1.22, 9.60], four seeds). The student also beat a teacher-label shuffle by +5.03 and a teacher-free arm by +4.57. Because the exact control holds the completion multiset fixed, unigram frequency and the teacher's label marginal cannot explain the gap; the signal rides on the prompt-to-token correspondence. A frozen audit found the carriers free of code, math, benchmark, and task terms.

The recovered signal is structured rather than generic. Code-trained and science-trained teachers produce their largest gains on their matching endpoints, with off-diagonal effects near zero or negative, and a 50/50 mixture of math and code shadows recovers both sources. Effect size tracks the teacher's update strength, and query *placement* matters more than volume: active near-tie selection beat a passive baseline by +4.27 points at the same teacher-query budget. Transfer also appeared in scientific knowledge, commonsense reasoning, and reading comprehension; mean gains were positive on Qwen3-1.7B, Qwen3-4B, and Llama-3.2-1B, though those confidence intervals include zero.

The limits are real: transfer was not reliable in every setting, and two "large-gap" teachers — one that memorized the HumanEval+ solutions, another given a substitution cipher — transferred nothing, because their advantage was not a capability the student could already express.

## Why It Matters

The standard mental model of post-training — a coding update improves coding — is too narrow. Updates also move the model's ordinary preferences on inputs that never mention the task. That breaks a common defence: filtering training data for task-specific content cannot stop capability leakage, because the leak is carried by *which word a prompt elicits*, not by visible semantics. The paper's own audit makes the point — a carrier set with no code terms still taught code.

ATD is also an extraction attack that needs no privileged access. No weights, no logits, no target-task examples — just one output token per query against a hosted model. Each query is a single bit, but thousands of them are cheap: the primary experiment used 5,664 tokens to lift a 1.5B student five points on an execution-scored benchmark. For anyone treating a private fine-tune as a trade secret, that is a new threat surface.

The same channel is a diagnostic, too. The shadow was measurable in the student before its task gains reached statistical significance, so it can expose aspects of an update that target-task scores miss — useful for model provenance, license enforcement, and detecting covert distillation between open-weight models that share an ancestor.

## Impact

Concretely, model providers get a new reason to care about output monitoring and rate limiting, not just content filters. Teams building on open-weight bases get a fingerprinting channel: if a competitor's model shares your ancestor and lifts on your domain, its unrelated word choices may reveal where the capability came from. Evaluation hygiene shifts as well, since benchmark-overlap scrubbing no longer covers a channel that lives in prompt-level token choices.

None of this is a universal theft vector. ATD assumes a known public ancestor and near-tie prompts, and it failed on non-elicitable teachers. But it reframes post-training as something that cannot be fully contained by task data — a shadow that follows the model onto every unrelated prompt, and one that, with enough single-bit observations, can be read back out.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Post-Training Leaves Behavioral Shadows on Unrelated Decisions (arXiv:2609.29233)](https://arxiv.org/abs/2609.29233) & [HuggingFace Daily Papers](https://huggingface.co/papers/2609.29233)*
