# Karpathy Was Right: The Best AI Engineers Are Automating Themselves Away

> Andrej Karpathy once joked that OpenAI researchers are "automating themselves away" by building ever-more-capable AI. A provocative essay by a version-control tool author suggests it was never a joke — it's the engineering blueprint for building reliable software with unreliable models.

## What Happened

In a sharp essay on his blog *Replicated*, developer Victor "gritzko" Grishchenko articulated a paradox every AI-assisted engineer eventually hits: LLMs are simultaneously brilliant and clumsy — and that won't change, no matter how many parameters get added.

Grishchenko builds Beagle SCM, a version control tool, with Anthropic's Claude. The model is remarkable at spotting bugs across mountains of code, filing tickets, and executing complex fixes. Yet yesterday it committed the same directory into a project — twice. His standing instructions say, in screaming all-caps: "DO NOT PARSE ANYTHING MANUALLY, EVER." It would be torturous and faulty. Claude tries anyway. Periodically he tells it to scan the codebase and surgically remove any manual parsing attempts it quietly inserted.

This isn't a model quality problem. LLMs are probabilistic pattern-matchers, not deterministic reasoners. They hallucinate, drift, and forget. As they scale, they get more brilliant — but "no less clumsy." The industry's default response, "just use a better model or a better prompt," misses the point entirely.

## Why It Matters

Grishchenko's answer is what he calls the **sandwich architecture**: deterministic tools below, a brilliant-but-clumsy LLM in the middle, and deterministic workflows above. The LLM handles what it does uniquely well — high-level reasoning, creative synthesis, pattern recognition across massive context windows. Everything else — parsing, compilation, verification, deterministic transformation — is handled by fast, reliable tooling.

The crucial twist is making the sandwich **malleable**. When Claude performs a sequence of actions too often, Grishchenko automates that sequence into a first-class tool. When it fails at something repeatedly, he automates the verification step. Over time, the LLM automates itself away — its successful patterns get captured as deterministic tools, and its failure modes get walled off by verification gates. The brilliance is preserved; the clumsiness becomes irrelevant.

This inverts the entire industry narrative. Instead of asking "how do we make LLMs more reliable?", the question becomes "how do we build systems where LLM unreliability doesn't matter?" The LLM becomes a replaceable intelligent component, not the brittle foundation of the stack.

## Impact

For working developers, this means the highest-leverage AI engineering skill isn't prompt design — it's tool architecture. The difference between a prototype that breaks in production and a system that ships reliably is the deterministic scaffolding around the model, not the model itself. A well-architected sandwich can absorb model swaps without architecture changes.

For the industry, the implications are profound. The most durable AI companies won't be model providers — they'll be the ones building the deterministic shells that make models safe and productive at scale. Code review tools that verify AI output before it hits main. CI pipelines that catch model drift. Verification layers that don't trust the LLM's self-assessment. The people "automating AI away" — systematically converting LLM capability into deterministic tooling — are building the infrastructure that outlasts any individual model release.

Karpathy's joke turns out to be the most practical AI engineering advice anyone has ever given. Build tools so good that the AI becomes unnecessary for the thing it just taught you to do.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Automating AI Away — replicated.live](https://replicated.live/blog/away) | HN Discussion: [101 points, 49 comments](https://news.ycombinator.com/item?id=48818937)*
