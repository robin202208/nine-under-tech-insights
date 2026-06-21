# The Accidental Recsys-ification of LLM Architectures

> Modern LLMs are looking more like Meta's terrifying recommendation systems every year. That's not an accident — it's the inevitable collision of capability demands with inference economics.

## What Happened

Back in 2022, the machine learning world at Meta had a clear divide. The LLM team worked with a clean, elegant stack of repeated Transformer blocks. The recommendation systems team, by contrast, maintained a sprawling, terrifying graph of specialized components. Fast forward to 2026, and Ian Barber's post on his blog captures a striking convergence: LLMs have become complicated now — deeply, structurally complicated.

Barber, drawing on his experience in both worlds, traces the architectural explosion across modern models. Compare Llama 3 to Nemotron 3 Ultra and the differences are stark. Attention mechanisms have splintered into query-grouped, compressed, sparse, linear, and sliding-window variants. Mixture-of-Experts routing, once confined to feed-forward layers, now governs attention blocks and even the residual stream. Vision and audio encoders aren't bolted on anymore — they're mixed directly into the architecture. And at inference time, models span multiple GPUs, introducing communication boundaries mid-architecture that reshape the optimization surface.

The culprit is the same force that drove recsys complexity: the tension between relentless capability improvements and brutal inference efficiency constraints. When every percentage point of throughput matters at scale, the "clean" architecture can't survive.

## Why It Matters

This isn't just academic architecture-watching. The recsys trajectory offers a warning: as these architectures get more complex, the gap between a conceptual model definition and a production-viable implementation becomes vanishingly small. You can keep a pure PyTorch definition as your research baseline, but once your training and testing loops consume significant resources, the performance optimizations — hand-fused kernels, custom communication patterns, operator scheduling — become load-bearing. You can't simply swap in a new attention variant and accept a 10% slowdown. Not when your competitor won't.

The dream that "agents will fix this" — that you'll hand your model definition to an AI and get optimally fused kernels back — misunderstands the problem. You need a performant baseline just to verify that the generated kernels are correct. And if generating that baseline requires hand-fusing your way through custom ops, you've already lost the flexibility that research iteration demands.

This is precisely what happened with recommendation systems. The architecture became too complex to casually modify. The research loop required a different kind of flexibility — composability designed in from the start, not retrofitted.

## Developer Impact

The practical implications are already materializing. Seb Raschka's model architecture comparison tool has become essential reading, not optional. Teams building on open models need to understand not just what architecture they're using, but which attention variant, which routing strategy, and which encoder mix — because swapping any of these changes the optimization surface completely.

For kernel development, FlexAttention in PyTorch shows one path forward: design composable, template-based attention primitives that let researchers explore with only mild performance impact, then specialize later. Andrej Karpathy's move to Anthropic, focused partly on auto-research loops at the frontier, signals that even the frontier labs recognize this as a first-order problem.

## Our Take

The recsys-ification of LLMs was predictable, but the speed is surprising. The winners in open-source AI won't be those with the flashiest architecture paper — they'll be the ones who solve composability, making it possible to iterate on architectures without rebuilding the optimization stack from scratch each time. That's hard, unglamorous work. It's also exactly what Meta's recsys teams spent a decade learning.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Ian Barber's Blog](https://ianbarber.blog/2026/06/19/llms-are-complicated-now/) | HN Discussion: [165 points, 57 comments](https://news.ycombinator.com/item?id=46738395)*
