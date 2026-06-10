# Anthropic Just Gave the Public Its First Taste of Mythos — and It's Called Claude Payble 5

**Date:** June 10, 2026  
**Tags:** #LLM #Anthropic #SoftwareEngineering #AgenticAI #AI-Safety

---

## What Happened

Anthropic launched Claude Payble 5 on June 10 — the first publicly available model derived from its previously locked-down Mythos series. Alongside it, the company simultaneously released Mythos 5, an unfiltered version available only to a narrow circle of vetted cybersecurity defense organizations and infrastructure providers.

The significance here is not just another model release. Mythos has been Anthropic's crown-jewel research line — models so capable in cybersecurity and biology that the company refused to release them at all. Claude Payble 5 is Anthropic's answer to a strategic question: can you take a frontier model powerful enough to find zero-days, add guardrails, and ship it to the world?

## The Numbers

Claude Payble 5 scored **80.3% on SWE-Bench Pro** — the demanding software engineering benchmark — and became the first model to cross **90% on the Hex analysis benchmark**. Anthropic positions it for complex research pipelines, long-term software development workflows, and long-horizon agentic tasks.

Pricing reflects the ambition: **$10 per million input tokens, $50 per million output tokens** — double the rates of Opus 4.8. Until June 22, it's available at no extra cost for Pro, Max, Team, and enterprise plans. After that, it moves to a paid credit system.

## What "Public Mythos" Actually Means

The original Mythos models found thousands of zero-day vulnerabilities across every major operating system and browser during Project Glasswing testing. Anthropic says Payble 5 has "new safeguards" blocking responses in high-risk cybersecurity and biology domains. The unfiltered Mythos 5 remains restricted to the 150+ organizations already participating in Glasswing.

This two-tier release model — a filtered public model plus an unfiltered defense-only version — is effectively a new category of AI deployment. It acknowledges that frontier capability and public release can coexist, but only through deliberate capability stripping.

## Who Gets Access

Claude Payble 5 is available through:
- Anthropic's own platform
- AWS (Bedrock)
- Google Cloud (Vertex AI)
- Microsoft Foundry

Notably, this is the first time a Mythos-derived model reaches all three major cloud providers simultaneously, signaling that Anthropic sees this as a mainstream enterprise product, not a research experiment.

## Why This Matters

This release challenges two assumptions at once. First, that frontier models must be either fully locked down or fully open — Anthropic is carving a middle path of "filtered frontier." Second, that safety and capability are a zero-sum tradeoff. Payble 5 is objectively more capable than Opus 4.8 on coding benchmarks, even with the safety filters in place.

For software engineering teams, the practical implication is immediate: a model that can handle multi-day development workflows with SWE-Bench Pro scores above 80% changes what "AI-assisted coding" means. We're moving from autocomplete to autonomous execution of complex engineering tasks.

The question Anthropic hasn't answered: how much capability did they remove to make Payble 5 "safe enough," and what happens when someone figures out how to jailbreak those guardrails? The 22-day free window is going to be a very busy period for red-teamers everywhere.
