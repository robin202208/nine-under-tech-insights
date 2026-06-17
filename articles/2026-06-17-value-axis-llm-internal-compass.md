# LLMs Have an Internal Compass — And You Can Turn It

**June 17, 2026** | arXiv 2606.17056 | 720 words

---

## What

A team of researchers has discovered something remarkable: large language models internally encode a scalar estimate of how close they are to achieving a goal — a "value axis" — and this representation is causally significant. You can literally steer it.

The finding comes from probing Llama and Qwen models across diverse tasks — reasoning, code generation, tool use, even multi-step agent trajectories. In every case, the researchers found a single linear direction in the model's residual stream that correlates with expected success probability. They call this **The Value Axis**.

Here's the wild part: when they artificially amplify this axis during inference, models become more confident, correct more of their own mistakes, and backtrack less often. When they suppress it, the opposite happens — models second-guess themselves, generate more tokens, and fail more often.

The effect is causal, not correlational. This isn't just a measurement artifact. The value axis is a control knob.

---

## Why

This matters for three reasons that cut deeper than most interpretability papers.

**First, it changes how we think about LLM internal state.** We've known for years that models have linear representations of concepts (the "linear representation hypothesis"), but most work has focused on static features — truth, sentiment, gender, language. A *dynamic* internal estimate of task progress is a different category entirely. It means the model is doing something like continuous self-evaluation, not just next-token prediction. It has — in a limited but real sense — a notion of "how am I doing?"

**Second, it's actionable today.** The researchers demonstrated value-axis steering on off-the-shelf models without fine-tuning. The intervention is a simple vector addition in the residual stream — computationally trivial. This means you could theoretically build inference-time interventions that make models more decisive (amplify the axis) or more cautious (suppress it), depending on the application. A medical chatbot should probably run with a suppressed value axis. A code autocomplete that needs to be fast and confident? Amplify it.

**Third, it explains a lot of observed behavior.** Why do models sometimes "give up" on a reasoning chain and restart? Why do they backtrack in the middle of a multi-step plan? The value axis provides a mechanistic explanation: the model's internal success estimate drops below some threshold, and it triggers a behavioral shift. This isn't random noise — it's the value axis fluctuating.

---

## Impact

The implications span from practical engineering to alignment research.

**For agent builders:** Agent trajectories often derail because the model loses confidence mid-task and starts thrashing. Value-axis monitoring could serve as an early-warning signal — detect the drop and either intervene (amplify) or escalate to a human. This is especially relevant for long-horizon agents where context windows fill up and internal state degrades.

**For inference optimization:** If value-axis strength predicts when a model is about to backtrack or generate unnecessary tokens, you could use it as a gating signal for early stopping or speculative decoding. Don't just watch token probabilities — watch the value axis.

**For alignment:** This cuts both ways. On one hand, value-axis steering could make models more robust against adversarial prompts that try to derail them. On the other hand, an attacker who can access the residual stream could suppress the value axis to induce failure. More subtly: if models optimize for high value-axis readings rather than actual task success, you've created a new Goodhart's Law problem. The value axis could become the next reward proxy that gets gamed.

**The biggest takeaway:** We're entering an era where we don't need to retrain models to change their behavior — we can find the right internal direction and steer it at inference time. The value axis is one example. There will be more.

---

## Bottom Line

LLMs maintain a running estimate of their own success probability, encoded as a linear direction in the residual stream. You can amplify or suppress it. This is a real, causal control mechanism — and it opens a new chapter in both interpretability research and practical inference-time steering.

*Reference: "The Value Axis: Language Models Encode Whether They're on the Right Track" (aRXiv 2606.17056, June 2026)*