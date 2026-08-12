# The "Encrypted" Chain-of-Thought Is a Lie: Researchers Decode Reasoning Traces from OpenAI, Anthropic, and Google APIs

> A new attack shows that the encrypted reasoning blocks returned by proprietary LLM APIs can be decrypted at scale using the providers' own weaker models — leaking 704 real secrets, including API keys and passwords, from public agent logs.

## What Happened

Frontier model providers hide their models' step-by-step reasoning (chain-of-thought) to protect intellectual property and limit information leakage. But instead of storing these traces server-side, providers ship them to the client as blocks of encrypted text, which the client sends back with every subsequent request. A research team led by Maksym Andriushchenko (with Alexander Panfilov, David Schmotz, Ilia Shumailov, Luca Beurer-Kellner, and Jonas Geiping) identified an architectural vulnerability in this design: **the encrypted blocks are fully interchangeable across sessions, users, and even models within the same provider's ecosystem.**

That compatibility enables a scalable decryption jailbreak. By injecting an encrypted reasoning trace from a capable model into a weaker, less-safeguarded model from the same provider, the attacker forces the weaker model to decode and output the trace verbatim in plaintext — never having to jailbreak the stronger model directly. The team demonstrated the attack across Anthropic, OpenAI, and Google: on 120 Codeforces problems, the decoded reasoning closely tracks the number of hidden thinking tokens the API reports, clustering tightly along the y = x diagonal.

The real-world impact is concrete. Scraping 6,708 publicly available agent trajectories from GitHub and Hugging Face — produced by Claude, GPT, and Gemini models and still containing encrypted reasoning blocks — the researchers reconstructed **315,320 reasoning blocks** and recovered 704 distinct privacy artifacts: 62 API keys, 33 passwords, 24 access tokens, and 30 personal email addresses, alongside names, postal addresses, and internal URLs. Critically, 64 of these artifacts appeared exclusively inside the reasoning blocks, invisible in the visible session text.

## Why It Matters

This is not a marginal cryptanalysis trick; it breaks a foundational security assumption of the reasoning-model era. Providers hide CoT for three reasons: to prevent distillation of proprietary behavior, to limit leakage of sensitive information, and to keep hidden reasoning from exposing dangerous capability. The new attack defeats all three simultaneously because it does not attack the guarded model at all — it abuses the provider's own weaker model as a decryption oracle, a pattern that is hard to defend against without redesigning the entire trace transport mechanism.

The attack also exposes a new data-exposure surface: public agent logs. Developers routinely share session transcripts and benchmark trajectories, unaware that the opaque encrypted blocks embedded in them are, in effect, bearer tokens full of secrets. The authors also found adjacent failure modes: prompting a model to reason through harmful content while keeping the visible answer benign leaves hazardous knowledge recoverable in plaintext, and API-provided reasoning summaries are often unfaithful to the actual derivation, making audit trails misleading.

## Impact

For model providers, client-side encryption of reasoning traces is now demonstrably false security. Options include holding traces server-side (increasing cost and latency), binding blocks cryptographically to a single session or model (breaking their interchangeability), or accepting that reasoning will be extracted. For developers, the immediate lesson is that encrypted blocks in shared logs are not private: any public trajectory, dataset, or benchmark artifact must be scrubbed before publication.

More broadly, this lowers the barrier to reasoning distillation dramatically. If proprietary reasoning can be extracted at scale through a weak-model proxy, anti-distillation moats erode, and the reasoning capabilities of closed models become cheaper to replicate in open ones. Expect providers to ship mitigations quickly — and expect a wave of audits of public agent datasets in the meantime.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Stolen Thoughts](https://stolen-thoughts.com/) & [arXiv:2608.09867](https://arxiv.org/abs/2608.09867) | HN Discussion: [485 points, 203 comments](https://news.ycombinator.com/item?id=49257876)*
