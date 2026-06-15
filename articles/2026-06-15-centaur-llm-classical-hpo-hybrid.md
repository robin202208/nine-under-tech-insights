# LLMs Still Can't Beat Classical HPO — But a 0.8B Model + CMA-ES Does

**Date:** 2026-06-15  
**Category:** Machine Learning / AutoML  
**Source:** arXiv:2603.24647 (Ferreira et al., ELLIS Institute Tübingen / University of Freiburg)

---

## What Happened

A team from the ELLIS Institute Tübingen and University of Freiburg put LLM-based hyperparameter optimization to a rigorous test — and the results are humbling for pure LLM approaches. Their paper benchmarks 9 HPO methods (4 classical, 4 LLM-based, 1 hybrid) on Karpathy's autoresearch task: tuning a ~50M parameter language model under a 24-hour GPU budget on a single H100, with 14 hyperparameters automatically extracted from the training script.

The verdict? Classical methods — CMA-ES and TPE — **consistently outperform LLM-based agents** when operating within a fixed search space. The gap isn't subtle: classical optimizers find better configurations faster, and LLM agents struggle with a specific, diagnosable failure mode — they cannot track optimization state across trials. Each trial starts fresh, with no accumulated knowledge of what worked or failed before.

Even frontier models don't close the gap. Pure LLM agents running Claude Opus 4.6 and Gemini 3.1 Pro Preview still fall behind classical methods. The one exception is Karpathy's original Agent (Code), which bypasses the fixed search space entirely by directly editing training source code — but even this approach merely achieves parity with classical methods, it doesn't surpass them.

Then comes **Centaur**.

## Why It Matters

Centaur is a hybrid that combines CMA-ES with an LLM by sharing the optimizer's **full internal state** — its mean vector, step-size, and covariance matrix — with the language model. This is the key insight: classical optimizers are excellent at tracking state across trials (CMA-ES maintains a multivariate normal distribution that encodes everything it has learned), but they lack domain knowledge about what hyperparameters mean. LLMs have rich domain knowledge but are effectively amnesiac across trials.

Centaur bridges this gap. The LLM receives CMA-ES's current distribution parameters as context, then proposes trials informed by both the optimizer's accumulated statistical knowledge and its own understanding of deep learning training dynamics. The results are striking: **a 0.8B parameter LLM running Centaur outperforms ALL classical methods and ALL pure LLM methods**, including frontier-scale models.

The scaling curve is revealing. For pure LLM agents, bigger models help marginally — the fundamental bottleneck is state tracking, not reasoning capacity. For Centaur, scaling from 0.8B to 27B to frontier models produces meaningful improvements, because the LLM is now augmenting a competent optimizer rather than trying to replace one.

## The Architecture

Centaur's design is deceptively simple. Each iteration:

1. CMA-ES updates its multivariate normal distribution based on previous trial results
2. The distribution parameters (mean μ, covariance Σ, step-size σ) are serialized into natural language
3. The LLM receives this state summary plus a history of recent trials and their validation losses
4. The LLM proposes the next hyperparameter configuration, which CMA-ES evaluates
5. The result feeds back into CMA-ES's distribution update

The ablation studies confirm that all three state components matter: removing the covariance matrix degrades performance more than removing the mean vector, suggesting that the LLM uses the uncertainty structure to decide where to explore.

## The Bigger Picture

This paper lands at a moment when "LLM agents for everything" is the default ambition. The authors' central finding — that LLMs are most effective as a **complement to classical optimizers, not a replacement** — generalizes beyond HPO. The pattern recurs across code generation (LLMs + symbolic verifiers), mathematical reasoning (LLMs + formal proof assistants), and database querying (LLMs + SQL engines).

For practitioners, the practical takeaway is clear: if you're using LLM agents for hyperparameter tuning today, you're leaving performance on the table. CMA-ES with a small LLM riding shotgun is cheaper, faster, and better than letting a frontier model fumble through the search space alone. The code and interactive demo are publicly available — the barrier to adoption is effectively zero.

The deeper signal is about architecture philosophy. The winning design pattern isn't "LLM as controller" — it's "LLM as informed advisor to a principled algorithm." That 0.8B model in Centaur isn't doing the optimization. It's reading the optimizer's mind and whispering better suggestions.

---

*Full paper: [Can LLMs Beat Classical Hyperparameter Optimization Algorithms?](https://arxiv.org/abs/2603.24647)*  
*Code: [github.com/ferreirafabio/autoresearch-automl](https://github.com/ferreirafabio/autoresearch-automl)*
