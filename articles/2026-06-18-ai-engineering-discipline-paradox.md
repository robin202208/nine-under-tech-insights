# AI Demands More Engineering Discipline, Not Less — And the Skeptics Are Wrong This Time

## What Happened

Charity Majors, co-founder of Honeycomb and a veteran of the DevOps revolution, published a sharp follow-up to her viral piece on AI and engineering. Her core argument: the people who are still dismissing AI-generated code as “slop” are making the same mistake the “server pets” crowd made during the infrastructure-as-code transition — holding a reasonable first-skepticism position long past its expiration date.

Majors traces the timeline precisely. Through most of 2025, the mainstream engineering position was that AI-generated code might never be good enough. That changed with Anthropic’s Opus 4.5 in November 2025, which she calls a tipping point rather than the sole cause. Behind it was a year of building: agentic harnesses that wrap LLMs in tool-use loops, function calling, MCP servers — an entire infrastructure layer that matured through 2025 and crested into genuine general-purpose usability by year-end.

The speed at which code went from “total crap” to “ah damn, that’s not bad” is what Majors wants engineers to internalize. The enthusiasts who said “this is coming faster than you think” were right. Holding out for “I’ll believe it when I see it” was forgivable the first time, she argues, but much less so now, as harness engineering and AI validation tools improve at the same exponential pace.

## Why It Matters

The crux of Majors’ argument isn’t that AI replaces engineering discipline. It’s the opposite: AI demands more of it. The skills that matter shift from writing boilerplate to system design, validation strategy, and harness engineering — the code that wraps LLMs in safe, testable loops.

This is where the DevOps parallel bites hardest. When infrastructure became code, the people who insisted on hand-crafting servers didn’t just become less productive — they became liabilities. Their manually configured boxes couldn’t be rebuilt, versioned, or tested. The same dynamic is now emerging with AI-assisted development. The engineer who refuses to use AI tools will soon be the one whose code can’t be reviewed at the scale and speed the organization demands.

But Majors is clear-eyed about what’s not changing. She explicitly rejects the notion that code review should be abandoned. The engineering practices that matter — testing, observability, canary deploys, incident response — become more critical, not less, when AI is generating code. The discipline just shifts upstream: from writing every line to designing the constraints within which AI operates, and building the validation systems that catch its mistakes.

## The Impact

**Short-term**: The “AI skeptic” position is losing its intellectual footing. As harness engineering matures and the evidence mounts, the burden of proof is shifting. Engineers who haven’t built a working mental model of AI-assisted development are falling behind — not on hype, but on measurable throughput.

**Medium-term**: Engineering organizations will need to redesign their onboarding, code review, and quality assurance practices around the assumption that a significant fraction of code is AI-generated. Not by lowering standards, but by raising them in the areas that matter: validation, architecture, and system-level thinking.

**Long-term**: The engineering identity itself is being renegotiated. If “writing code” is no longer the core value proposition of a software engineer, what is? Majors’ implicit answer: the engineer becomes the person who designs the system, defines the constraints, and builds the safety rails — not the person who types every line. That’s a more demanding role, not a diminished one.

---

*Published: 2026-06-18 | Source: [Charity Majors — "AI demands more engineering discipline. Not less"](https://charitydotwtf.substack.com/p/ai-demands-more-engineering-discipline) | HN Discussion: [383 points, 188 comments](https://news.ycombinator.com/item?id=45891720)*
