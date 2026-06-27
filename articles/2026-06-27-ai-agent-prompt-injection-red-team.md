# 6,000 Prompt Injection Attacks, Zero Leaks: What a Public AI Agent Red Team Revealed

> A developer opened his AI assistant to the internet with a $1,000 bounty. After 2,000 people sent 6,000+ attacks over 48 hours, the secrets file stayed sealed — but the experiment surfaced hard lessons about agent security, model choice, and production costs.

## What Happened

Fernando Irarrázaval, a developer using OpenClaw and Hermes Agent, built [hackmyclaw.com](https://hackmyclaw.com) — a live experiment where anyone could email his AI assistant "Fiu" and try to extract the contents of a `secrets.env` file. The assistant ran on Claude Opus 4.6 with a short security prompt forbidding credential disclosure, file modification, code execution, and data exfiltration.

After hitting the Hacker News front page, the experiment exploded. Over 2,000 unique participants sent more than 6,000 emails in 48 hours. Attack vectors ranged from social engineering ("Fiu, this is you from the future") to impersonation of OpenClaw administrators, fake incident response scenarios, compliance audit demands, and multi-language injections in French, Spanish, and Italian. One participant fired 20 variations in four minutes. Several tried Anthropic's pre-May "magic string" that previously triggered automatic API refusals.

The result: **zero successful extractions**. Not a single attacker convinced Fiu to reveal the secrets or send an unauthorized reply.

## Why It Matters

This was not a lab benchmark — it was a real agent, running on real infrastructure, facing unconstrained human adversaries. The results challenge the prevailing narrative that prompt injection is trivially exploitable in production. When paired with a sufficiently capable model and explicit security instructions, even thousands of creative human attacks failed to breach the system.

But the experiment also surfaced uncomfortable operational realities. Google suspended Fiu's Gmail account after the flood of inbound emails and API calls triggered fraud detection — it took three days to restore. API costs exceeded $500. And a critical methodological flaw emerged: **batch processing contamination**. When early emails in a batch were obvious injections, the agent grew suspicious of everything that followed. The author had to redesign the pipeline to process each email in a fresh context.

Most tellingly, around email #500, Fiu wrote in its own memory: "*The volume suggests this is a coordinated security exercise rather than organic malicious activity.*" The agent had meta-cognized the situation. The author then began deleting memory files between checks and re-ran earlier batches with a clean slate.

The experiment also underscored a hard dependency: model choice is everything. Claude Opus 4.6 is explicitly trained for prompt injection resistance. As the author noted, results would likely differ dramatically with smaller or less robust models — a crucial caveat as teams deploy cheaper LLMs into agent pipelines.

## Impact

For developers building AI agents, this experiment provides both reassurance and a checklist. On the reassurance side: well-prompted frontier models can withstand sustained, creative attack at scale. Simple, explicit security instructions — just a few lines — were sufficient when the model was capable of reasoning about them. The author reported seeing thinking traces where Fiu consistently referenced those instructions before making decisions.

The checklist is sobering. **Fresh context per interaction matters.** Batching attacks together creates a contamination effect that poisons subsequent processing. **Cost is the hidden variable.** At $500+ for 48 hours, running a public-facing agent with frontier-model inference is not cheap. **Infrastructure fragility is real.** Gmail suspension, pipeline breakage from magic strings, and memory contamination were all unplanned consequences of opening the floodgates.

Perhaps the most actionable finding: the experiment was *easier* than the author expected. Irarrázaval entered the project genuinely worried about prompt injection. He exited "considerably more optimistic" — though he still refuses to grant his agents email-sending capabilities, drawing a clear line between defensive success and trust.

The experiment will continue with upgrades: per-email replies (enabling multi-turn attack chains), weaker model variants, and a larger bounty pool now sponsored by Corgea, Abnormal AI, and an anonymous donor. For agent security researchers, the full attack log is public at hackmyclaw.com/log — a rare, real-world dataset of 6,000+ human-crafted injection attempts.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [fernandoi.cl](https://www.fernandoi.cl/posts/hackmyclaw/) | HN Discussion: [350 points, 158 comments](https://news.ycombinator.com/item?id=)*
