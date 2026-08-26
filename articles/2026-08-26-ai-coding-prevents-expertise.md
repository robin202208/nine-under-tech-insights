# AI Coding Tools Demand the Expertise They Destroy

> The tools promising to democratize programming may be quietly dismantling the pipeline that produces expert developers — and the evidence says the people leaning on them hardest are the ones learning the least.

## What Happened

A widely discussed essay by Lars Faye (539 points, 535 comments on Hacker News) argues that reliance on AI coding assistants is preventing the formation of software engineering expertise. The argument rests on a growing body of empirical work. A study cited by JetBrains — *The Widening Gap: The Benefits and Harms of Generative AI for Novice Programmers* — observed novice developers in live coding sessions. Participants who leaned heavily on AI "often skipped crucial planning stages" and finished with an "illusion of competence" rather than real understanding; the best-performing novices were those who mitigated or outright ignored the assistance. The ones who succeeded had developed what the researchers call "negative expertise" — the ability to ignore incorrect or unhelpful AI suggestions.

A UPenn study published in PNAS followed 1,000 students using LLMs to learn mathematics: the AI-assisted group performed 17% worse than a textbook-only group, while believing they were excelling. Anthropic's own 2026 research (*How AI assistance impacts the formation of coding skills*) reached a similar conclusion — cognitive effort, "and even getting painfully stuck, is likely important for fostering mastery."

## Why It Matters

Faye names the paradox precisely: the skills required to wield AI coding agents responsibly — architecture, spec-writing, critical review — are exactly the skills that continuous AI use erodes. A novice "needs expert-level skills to leverage the tools," yet the tools circumvent the friction that builds expertise. He calls the resulting dynamic "inverted learning": the student steers the mentor without knowing what questions to ask, and the model becomes a compass that always points north, wherever you suggest north might be. Even well-prepared students get derailed — one skipped planning entirely, jumped straight to code, and then had to rely on the LLM to fix an error the LLM itself had introduced.

The deeper point is that expertise is not information. It is *Fingerspitzengefühl* — the intuition built from tracing obscure errors and watching approaches fail to scale. Spolsky's Law of Leaky Abstractions applies: "abstractions save us time working, but they don't save us time learning." If generation replaces struggle, the pipeline that creates senior developers dries up just as the code being generated today accumulates tomorrow's maintenance burden. Sentry co-founder David Cramer is blunt about the trillion-dollar bet that models will get good enough to clean up their own junk: "I don't think that's true. I think it's a science experiment."

## Impact

For developers, the prescription is counterintuitive: use AI as a Socratic sparring partner, not an answer generator. The same UPenn study found that a "tutor" mode — students ask for help, then solve independently — improved AI-assisted practice performance by 127%, although it did not improve final test scores. The operative distinction is between cognitive debt (abdicating your judgment) and cognitive offloading (delegating the mechanical).

For companies mandating AI-first workflows, the message is a risk warning: forcing juniors to generate rather than understand trades short-term throughput for a decimated talent pipeline. For tool builders, features that hide code or optimize purely for generation speed optimize against learning. As François Chollet puts it, LLMs are interpolation engines — but software engineering is adaptation and novel problem-solving. The generation that inherits today's AI-written code without ever struggling through its failures will be the first that cannot fix what it did not write.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Coding expertise is going to collapse from AI reliance (Lars Faye)](https://larsfaye.com/articles/ai-coding-will-prevent-expertise) | HN Discussion: [539 points, 535 comments](https://news.ycombinator.com/item?id=49421554)*
