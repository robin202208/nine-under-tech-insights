# Reasoning Doesn't Need More Depth. It Needs More Width.

> An 8B model just beat GPT-5 on one of the hardest math contests in the world — not by thinking harder, but by thinking in parallel.

## What

StepFun and Tsinghua University just published **PaCoRe (Parallel Coordinated Reasoning)** at ACL 2026, and the headline number demands attention: an 8-billion-parameter model scored **94.5% on HMMT 2025**, surpassing GPT-5's 93.2%. How? By doing something that sounds obvious in retrospect but required rebuilding the entire reasoning paradigm.

Every major reasoning model today — GPT-5, DeepSeek-R1, Claude — works the same way: a single chain of thought that grows longer as the problem gets harder. More tokens, more reasoning. The problem is that this chain lives entirely inside a fixed context window. When that window fills up, reasoning stops. The model can't think anymore.

PaCoRe breaks this coupling. Instead of one long chain, it launches **dozens of parallel reasoning trajectories simultaneously** — each exploring a different angle of the problem. After each round, it compacts each trajectory's findings into a short message, feeds all the messages back into the model, and synthesizes them to guide the next round. Think of it as a team of mathematicians all working on different approaches, then huddling up every few minutes to share what they've found before diving back in.

The architecture is a **multi-round message-passing system** that sits on top of a standard LLM. Round 1: launch K parallel trajectories. Compaction: each trajectory's insights get compressed into context-bounded messages. Synthesis: the model reads all messages and decides where to explore next. Round 2, 3, N: repeat, with each round informed by everything discovered so far. The total "effective test-time compute" — the sum of all tokens across all trajectories — reached roughly **2 million tokens** for the highest-performing configuration. But the model never held more than a few thousand tokens in any single context window.

And here's the twist that makes it work: the model had to be **trained to coordinate**. When the researchers first tried feeding parallel insights to a standard reasoning model, they discovered what they call **"Reasoning Solipsism"** — the model would ignore the rich insights from parallel branches and try to solve the problem from scratch, wasting all that accumulated compute. They fixed this by training the model end-to-end with large-scale outcome-based reinforcement learning, explicitly rewarding it for synthesizing parallel findings rather than going solo.

## Why

This matters because the sequential chain-of-thought paradigm has been hitting a wall. Context windows are growing (1M tokens and counting), but so is the ambition of reasoning tasks — multi-hour research problems, codebase-level refactoring, theorem proving. You can't just make the chain longer forever. The context window becomes the reasoning ceiling.

PaCoRe changes the scaling curve from **linear to parallel**. Instead of reasoning quality being bounded by how many tokens fit in a single window, it's bounded by how many parallel trajectories you can afford to run. And parallel trajectories are embarrassingly parallelizable — they can run across different GPUs, different machines, even different providers.

The key insight is that **breadth beats depth for complex reasoning**. The researchers found that increasing parallel trajectories (width) consistently outperformed making individual chains longer (depth) for the same total compute budget. On HMMT 2025, going from 4 trajectories to 32 boosted accuracy from 88.2% to 94.5%. On LiveCodeBench, a standard RLVR-8B model flatlined as test-time compute increased — it simply couldn't use more tokens effectively. PaCoRe-8B, trained for coordination, kept improving as compute scaled up.

StepFun open-sourced everything: model weights, 8K training examples, and the full inference pipeline including a server that works with any LLM endpoint. This is not a research demo — it's a deployable system.

## Impact

First, **the economics of reasoning models just changed**. An 8B model with parallel coordination outperforms a 400B+ frontier model on specific benchmarks. The inference cost difference — even accounting for parallel trajectories — is measured in orders of magnitude. If this generalizes beyond math (and the LiveCodeBench results suggest it does), we're looking at a future where specialized small models + coordination frameworks match or exceed monolithic giants on structured reasoning tasks.

Second, **"Reasoning Synthesis" is a new capability to train for**. We've spent years optimizing for single-chain reasoning quality. PaCoRe shows there's an entirely separate skill — the ability to read multiple partial solutions, identify promising directions, and synthesize them into better solutions — that standard RL training doesn't teach. Models trained for synthesis outperform models trained for solo reasoning, even on the same base architecture. This suggests a new dimension in the post-training pipeline.

Third, **the coordination framework is model-agnostic**. The PaCoRe server works with any LLM endpoint. You can plug in a cheap model for exploration rounds and a stronger model for synthesis rounds. You can mix providers. The coordination layer becomes a commodity, and differentiation shifts to how well your model synthesizes parallel insights — not how long its chain of thought is.

The formula that dominated the last two years — "scale parameters, scale context, scale chain length" — just got a third axis. Width.

> *Sources: Hu et al., "PaCoRe: Learning to Scale Test-Time Compute with Parallel Coordinated Reasoning" (ACL 2026, arXiv:2601.05593); PaCoRe GitHub (stepfun-ai/PaCoRe, 337 stars); StepFun HuggingFace model release.*
