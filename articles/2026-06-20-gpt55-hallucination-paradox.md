# The Hallucination Paradox: Why Bigger Models Lie More

> A new benchmark reveals GPT-5.5 and DeepSeek V4 Pro hallucinate at rates far exceeding smaller open-weight alternatives — challenging the assumption that scale solves everything.

## What Happened

A new benchmark called AA-Omniscience, published by AI testing platform ArrowTSX, has produced a startling result: GPT-5.5 and DeepSeek V4 Pro rank among the worst hallucinators tested. DeepSeek V4 Pro — a 1.6 trillion parameter model with 49 billion active parameters — scored a 94% hallucination rate on questions it could not answer. In other words, when it did not know the answer, it admitted ignorance only 6% of the time.

By contrast, GLM-5.2, an MIT-licensed model from Tsinghua's Zhipu AI, hallucinated roughly three times less than GPT-5.5 on the same benchmark, despite being a fraction of the size. The test suite covers factual recall, logical reasoning, and technical problem-solving — domains where hallucination carries real consequences.

The finding is counterintuitive. For years, the industry narrative has been that larger models, trained on more data with more compute, produce more reliable outputs. The AA-Omniscience data tells a different story: at the frontier, hallucination rates are not falling — they are rising.

## Why It Matters

The root cause appears to be a training artifact. When a model is trained on vast volumes of highly factual, authoritative data, it learns a pathological behavior: always produce an answer. The model internalizes that silence or uncertainty is never the correct response, because its training distribution contains few explicit demonstrations of saying "I don't know."

This is not a new insight — researchers have warned about "sycophancy" and "overconfidence" in large models for years. But the AA-Omniscience results quantify the gap in stark terms. A 94% hallucination rate on unknown questions means the model fabricates answers 15 times for every single honest admission. For any application involving truth-critical decisions — medical advice, legal analysis, financial reasoning — that ratio is unacceptable.

The implications extend beyond accuracy metrics. If the largest, most expensive models are also the least trustworthy on unfamiliar territory, enterprises face a difficult calculus. Do they pay premium API prices for models that confidently produce wrong answers? Or do they adopt smaller, open-weight models that demonstrate better calibration?

## Developer Impact

For builders, the message is clear: model size is not a proxy for reliability. Before integrating any model into a production system, test it on your specific domain with a hallucination benchmark. GLM-5.2's strong performance on AA-Omniscience suggests that the open-weight ecosystem is closing the reliability gap — and doing so with MIT-licensed models that carry no API dependency.

The benchmark also underscores the importance of retrieval-augmented generation and structured output constraints. Even the best models benefit from grounding in verified data sources and schema-enforced responses that leave no room for fabrication.

## Our Take

The hallucination paradox should reset industry expectations. Scale alone does not produce trustworthy AI. The models that win in production will be those that combine robust factual grounding with the humility to admit ignorance — and the emerging open-weight contenders are proving surprisingly capable at both.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
