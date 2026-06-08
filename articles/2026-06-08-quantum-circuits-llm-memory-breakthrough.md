# Quantum Circuits Just Ran Inside a Production LLM — And It Actually Worked

> For the first time, a portion of a widely-used large language model has been replaced by quantum circuit components and executed on real quantum hardware while generating text. Multiverse Computing ran quantum blocks inside Meta's Llama 3.1 8B on IBM's 156-qubit processor — and the model got better, not worse.

## What Happened

On June 4, 2026, a team led by Borja Aizpurua at Multiverse Computing in San Sebastián, Spain, published a preprint demonstrating something no one had done before: running a production-scale language model with actual quantum circuit components on real gate-based quantum hardware — and measuring a genuine improvement.

The model was Meta's Llama 3.1 8B-Instruct, one of the most widely deployed open-source LLMs. The quantum hardware was IBM's Quantum System Two, a 156-qubit superconducting processor. The result: a 1.4% reduction in perplexity on WikiText, a standard measure of how well a model predicts text.

What makes this remarkable isn't the number itself — 1.4% sounds modest. It's what it cost to achieve: only 6,000 additional classical parameters. Compare that to scaling a model from 8B to 70B parameters, which might yield similar improvements but requires nearly 9× the memory and compute.

## The Technique: Cayley-Parameterized Unitary Adapters

The core innovation is deceptively simple. Rather than trying to quantumize an entire LLM — a fool's errand given current hardware — the team inserted small quantum circuit blocks called "Cayley-parameterized block-diagonal unitary adapters" into the frozen projection layers of the pre-trained model.

Think of it as surgically implanting quantum components into specific layers of a classical neural network. The classical model runs on GPUs as usual. When data hits those specific layers, it gets routed through a quantum circuit on IBM's hardware, and the result flows back into the classical pipeline.

The construction is hardware-efficient by design. Instead of synthesizing generic unitary transformations — which require exponentially deep circuits — the block-diagonal structure factorizes into independent 4×4 blocks that can execute as shallow, parallel circuits. Each 4×4 block needs only six free parameters. The total circuit depth on IBM's `ibm_basquecountry` processor was just 19 native gates.

This is not simulation. This is not theoretical. The quantum components ran on actual superconducting qubits.

## The Phase Transition Nobody Expected

The team also ran systematic experiments on SmolLM2, a 135M-parameter model, varying the unitary block dimension to understand what was happening. They discovered a "sharp noise-expressivity phase transition" — a point where the benefits of quantum computation overtake the degradation caused by hardware noise.

This transition matters enormously. It means that even modest increases in qubit count and coherence time could unlock disproportionately large gains. At this phase transition, SmolLM2 not only showed better perplexity but provided correct answers to questions that purely classical baselines got wrong.

They also demonstrated 83% recovery of compression-induced degradation — meaning these quantum adapters could mitigate the performance loss from model compression techniques like quantization and pruning. If you can compress a model 4× and recover 83% of the lost performance with 6,000 quantum-tunable parameters, the cost equation for AI deployment shifts dramatically.

## Why It Matters

The LLM industry is running into a wall. Every additional parameter costs memory, compute, and energy — and the scaling laws that delivered GPT-3, GPT-4, and beyond are showing diminishing returns. Quantization and pruning help, but they trade away capability.

Quantum circuits offer a fundamentally different scaling path: use the exponentially larger state space of qubits to enhance model expressivity without adding classical parameters. Each qubit can represent a superposition of states — a density of information impossible in classical bits.

The numbers are small today. A 1.4% perplexity gain won't transform the industry tomorrow. But the significance is qualitative, not quantitative. This is the first time a production LLM has been genuinely enhanced — not degraded — by running through real quantum hardware. It's analogous to the first demonstration of Shor's algorithm factoring the number 15: a proof of concept that establishes the pathway.

Multiverse Computing raised $215 million in 2025 for its quantum-inspired compression technology. This preprint shows why investors are paying attention. The company isn't waiting for fault-tolerant quantum computers — they're extracting value from noisy, intermediate-scale quantum (NISQ) hardware today.

The implication for AI developers is clear: the next generation of model efficiency might not come from better GPUs or cleverer architectures. It might come from a completely different physics.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
