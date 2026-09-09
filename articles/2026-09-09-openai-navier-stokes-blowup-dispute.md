# AI Claims a Navier–Stokes Blowup Proof in 88 Hours — and a Credit War Erupts

> An unreleased OpenAI model produced a claimed finite-time blowup proof for the Navier–Stokes equations with a Lean 4 certificate — on the same day two mathematicians released their own AI-assisted blowup results, alleging their private progress had been used against them.

## What Happened

On September 8, NYU mathematician Tristan Buckmaster and Anthropic mathematician Levent Alpöge released three proofs: finite-time blowup with smooth forcing for the incompressible porous media equation, the Boussinesq system, and 3D incompressible Euler — building on a program pioneered by Diego Córdoba and Luis Martínez-Zoroa. Their work is "heavily AI-assisted," as Terence Tao noted, using Claude and OpenAI's Codex; they verified their first LLM-generated proof in Lean on August 22. Buckmaster says they also have blowup for hypo-dissipative Navier–Stokes but are withholding it until Lean verification finishes.

Hours later, OpenAI published "On the Navier–Stokes Millennium Prize Problem": an AI-generated solution claiming that for every viscosity ν > 0 there exists a smooth, compactly supported forcing term and smooth velocity/pressure fields whose kinetic energy stays bounded yet whose velocity blows up in L∞ as t → 1 — transported to the torus by rescaling-and-periodization. The proof runs roughly 165 pages and ships with a Lean 4 formalization on GitHub. According to MathOverflow analysis, the claim targets options (C)/(D) of Fefferman's official Clay statement — the counterexample route where a smooth external force is permitted — not options (A)/(B), the "does turbulence spontaneously blow up" question, which remains untouched. OpenAI says it is not claiming the Clay Prize.

The scale is unprecedented: roughly 10,000 concurrent agents reached the result about 88 hours after launch, plus 17 hours for Lean verification, consuming an estimated 300 billion output tokens — about $22.5 million at Astra rates. OpenAI says the effort began September 1 after rumors that Millennium problems had been solved.

## Why It Matters

The mathematics is a genuine frontier event even with the forced/unforced caveat. Blowup with smooth forcing for Euler was previously open; the Córdoba–Martínez-Zoroa strategy — iteratively adding high-frequency corrections that make solutions more singular while keeping the forcing benign — has now been pushed, partly by LLMs, to a claimed Navier–Stokes result with a machine-checkable certificate. If verified, this is the first AI-assisted attack on a Clay Millennium problem, a "Deep Blue–Kasparov moment," as Buckmaster puts it, with the added twist that Lean 4 gives a binary verdict independent of lab reputation.

The controversy is equally significant. Buckmaster's public statement alleges that after their progress leaked to OpenAI, an internal team raced a model to the same result — the first prompt arriving only after information about their work had reached the company. He recounts being told the model did not look up user data but says the training question went unanswered, and that OpenAI proposed removing Alpöge — an Anthropic employee — from authorship, with remarks like "Why would you ruin your career?" He is careful: "I am not accusing anyone of anything. I am stating what I was told."

OpenAI's response confirms the timeline began September 1, denies researchers or agents saw Buckmaster's work, and concedes: "While unlikely, we cannot rule out that de-identified data derived from their usage of our products helped improve our models." That is the crux — Buckmaster and Alpöge had been feeding all their drafts into Codex sessions, and OpenAI reserves the right to train on Codex interactions. When frontier research is conducted inside a vendor's tools, the vendor's next model may know more than it admits.

## Impact

For mathematicians, the forced-blowup frontier just moved from rough to smooth forcing across multiple equations, with hypo-dissipative Navier–Stokes and unforced Euler as the next rungs — and Buckmaster argues Martínez-Zoroa deserves a Fields Medal for the underlying program. For AI labs, this is the sharpest demonstration yet that frontier models can now "mine" circulating ideas at $22.5 million per week, compressing years of human effort into days.

For researchers, the chilling effect is real: doing secret frontier work inside commercial coding agents now carries provenance risk, and Tao notes the human authors were forced to release undigested preprints — one writeup Buckmaster calls "AI slop" — purely because of external events. The community must now referee ~165-page, machine-generated PDE arguments, decide how credit flows when human intuition and model compute are inseparable, and confront that the most famous open problems in mathematics can be raced rather than solved. The proof itself remains unverified; but the era it signals is already here.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [OpenAI — On the Navier–Stokes Millennium Prize Problem](https://openai.com/index/navier-stokes-solution/) & [Tristan Buckmaster's statement (PDF)](https://cims.nyu.edu/~tristanb/statement.pdf) & [TechCrunch](https://techcrunch.com/2026/09/08/openai-fought-dirty-on-career-making-math-problem-says-nyu-mathematician/) | HN Discussion: [1,088 points, 948 comments](https://news.ycombinator.com/item?id=49613262)*
