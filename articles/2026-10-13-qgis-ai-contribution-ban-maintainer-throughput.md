# QGIS Banned AI Contributions — and the Reason Isn't Code Quality

> A major open-source GIS project just replaced its "human in the loop" AI policy with a hard ban. The strongest argument wasn't about buggy code — it was about a resource no model can generate: maintainer attention.

## What Happened

On October 7, 2026, the QGIS project merged PR #387 in its enhancement-proposal repository, rewriting QEP-408 — its "AI tool use policy" — from a permissive rule into a prohibition.

The original, authored by Even Rouault on January 31, 2026, allowed AI use with one condition: "there must be a **human in the loop**." Contributors had to read and review all LLM-generated code before asking anyone else to, remained "fully accountable," and had to "be able to answer questions about their work" during review — because requesting a review was supposed to be "a good use of scarce maintainer... time."

Version 2.0, dated September 24 and credited simply to "the QGIS project contributors," deletes all of that. The replacement is four sentences long: "No LLM, AI or agent assisted code contributions are allowed in the https://github.com/qgis/qgis repository. If we subsequently discover that you have used an AI, LLM, or any agent-based workflow for your contribution, you will be subject to code reversion and contributor rights sanctioning."

It was contested: 88 thumbs-up, 25 thumbs-down. PSC member Tim Sutton framed it as a "moratorium on LLM use," paired with a proposal requiring a "proof of life" from new contributors — the discussion floated a build screencast or a signed CLA. The debate ran through the qgis-developer mailing list and a companion pull request (#67509).

## Why It Matters

The stated rationale is not that AI writes bad code. It is that AI writes *too much* of it. As contributor Haubourg put it, "AI use from external contributors puts a massive and unbearable pressure on the shoulders of our maintainers, who already had a tough job before the era of LLMs." The volume of AI-assisted patches now exceeds what reviewers can absorb, forcing a choice between an unreviewed backlog and review so shallow it is theater — both of which corrode a project whose value rests on trust that its code was examined by someone who understood it.

The first policy's failure is measurable. Maintainer uclaros noted that the promised "extractive" tag was never created, roughly 50 PRs were labeled AI-assisted, about 40 of those were closed unmerged — "and there are several others that were simply closed but never tagged." QEP author Nyall Dawson said the project had "already lost at least one extremely high value contributor as a result" — not to AI code, but to the friction of adjudicating it case by case.

What is really being priced here is a trust contract. One contributor who submitted two AI-assisted bug-fix PRs made the point without defensiveness: "I'm clearly not yet skilled enough to fix bugs in a project as complex as QGIS, but LLMs give you the illusion that it's possible... we've broken a trust contract between the reviewer and the contributor. The contract that, to contribute at such a big project, you know what you're doing." Free-software projects also depend on a human network — organizations fund contributors for support and features — and an AI firehose can sever it.

The counterargument is that the ban is too broad, and the project's own leaders concede it. Contributor kannes warned against "throwing the baby out with the bathwater": LLMs are useful for reading unfamiliar C++ context, or as a simulated reviewer before you bother a human. Rouault's reply is the clearest statement of intent: the baby "might be non-viable, so... This QEP is a stop gap measure... We might have to wait for the burst of the AI bubble to fully understand what a fair AI use might be." Dawson added the door is shut "so we can then selectively open a little" — ultra-restrictive now, case-by-case later. One member observed that 100% of recent security advisories were already AI-written — though those concern QGIS's websites, not the core.

## Impact

For maintainers, QGIS is now a template: a policy that names the real constraint — reviewer attention — instead of litigating code quality. Expect other projects to copy the structure, and the enforcement machinery (CLAs, "contributor rights sanctioning") that turns an honor system into something enforceable.

For contributors, the advice is blunt: if you cannot explain a patch line by line, do not submit it to a project that has banned AI assistance. And the fairness question will not go away: as Célia Buira put it, the community spent three decades building a commons that LLM companies took for free, "and make us pay twice" — once for access, again through pressure on infrastructure.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [QGIS QEP-408 AI tool policy update (PR #387)](https://github.com/qgis/QGIS-Enhancement-Proposals/pull/387) | HN Discussion: [363 points, 398 comments](https://news.ycombinator.com/item?id=50054751)*
