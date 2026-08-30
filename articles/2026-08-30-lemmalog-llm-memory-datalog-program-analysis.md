# LLM Memory, Rebuilt as Program Analysis: Why a Datalog Engine Beats a Bigger Context Window

> An open-source experiment called Lemmalog treats an agent's long-running knowledge the way a static analyzer treats a program — facts, rules, retractions, provenance — and beats full-context prompting by 2× on memory benchmarks while using 38× fewer tokens.

## What Happened

Jordy Zomer, a security researcher at pwning.systems, kept hitting the same wall using LLM agents for multi-hour vulnerability investigations: the model slowly loses track of what was actually established. It re-suggests approaches already ruled out, forgets that an assumption turned out false, and reasons confidently from invalidated observations. Telling an LLM that something is wrong does not stop it from believing everything that depended on that something.

His conclusion was that "memory" hides two different problems. The first — *what information from the past is relevant to this question?* — is what vector databases solve well. The second — *given everything we've learned so far, what is currently true?* — is a state-maintenance problem, and retrieval systems are structurally bad at it: a vector DB will happily return a fact you disproved two hours ago because it is semantically relevant, with no idea that five conclusions depended on it and are now invalid.

So he built [Lemmalog](https://github.com/JordyZomer/lemmalog), a Datalog engine for LLM agents. The LLM handles the fuzzy part — turning source code, debugger output, and natural-language notes into structured facts. Lemmalog handles the deterministic part: deriving new facts through rules, computing a fixed point of what is known, and — crucially — supporting retractions. If an observation changes, only the conclusions derived from it are invalidated, and a conclusion supported by two independent paths survives the loss of one. Because every derived fact tracks its derivation, you can also ask the agent *why* it believes something, exposing provenance for claims like "we already established this pointer is attacker-controlled." Facts carry validity intervals, so the system knows both what is true now and why it once believed something wrong — and an MCP server lets agents consume the engine directly.

## Why It Matters

The benchmark results make this more than a clever toy. On LongMemEval (102 questions), Lemmalog scores F1 0.463 ± 0.010 — versus 0.550 for PropMem, 0.480 for SimpleMem, and 0.222 for full-context prompting (the author's own GPT-4.1 full-context run scored 0.197). On LoCoMo, a much larger 1,986-question benchmark, it lands at 0.533 ± 0.001, run three times with near-identical results. It is not the overall winner yet — PropMem still edges it — but it wins exactly where its design says it should.

Knowledge Update is the category that most resembles maintained program state: *we believed A, later we learn A is no longer true, what should we believe now?* Lemmalog tops the published field at 0.579, versus 0.528 for PropMem and 0.202 for full context. On adversarial false-premise questions it scores 0.707 against 0.509 for full context — a structured memory can simply answer "no" when no supporting fact exists, where full context is tempted to match semantically similar stories. And the cost story is striking: LongMemEval questions shrink from ~104,000 tokens to ~2,700 (38×), LoCoMo from ~18,900 to ~3,400 (6×). Extraction happens once; full-context prompting pays for the entire history on every query, so the gap grows without bound as agents run longer.

The journey also exposes how much of "smarter AI" is engineering hygiene. F1 doubled from 0.226 to 0.463 through fixes like: dates compared as interned Datalog symbols (internal IDs, not timestamps — a 20-point temporal jump when fixed), entity reconciliation across sessions, a plural stemmer that never matched "owns" to "own," and a hallucination-reduction instruction that taught the reader to refuse 32 of 102 answerable questions.

## Impact

The most important implication is architectural: bigger context windows are not the only answer when agents forget things — sometimes you can just *maintain the state*. For long-horizon agent tasks — vulnerability research, codebase audits, anything running for hours — a hybrid design of deductive state plus episodic memory (raw source text for nuance) may be the durable pattern, with LLMs doing probabilistic parsing up front and deterministic engines doing the bookkeeping.

For developers, Lemmalog is a reminder that the hard part of agent memory is not computing conclusions but building a good information-retrieval layer from natural language — and that dependency tracking, invalidation, and fixed points are problems we have solved for decades. It is open source, benchmarked transparently (including the failures), and honest about its weaknesses: inference-heavy questions still trail PropMem badly. But as agents graduate from chat to multi-hour autonomous work, "memory as a database problem" will only get more relevant.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [pwning.systems](https://pwning.systems/posts/llm-memory-program-analysis/) & [Lemmalog on GitHub](https://github.com/JordyZomer/lemmalog) | HN Discussion: [277 points, 73 comments](https://news.ycombinator.com/item?id=49485416)*
