# An Agent Swarm Found a CRISPR-Like Enzyme System Nobody Was Looking For

> Anthropic gave roughly 950 Claude agents one prompt and 21 hours of DNA-database search. They came back with a previously uncharacterized biological system hiding in bacteriophages.

## What Happened

On September 23, Anthropic's life sciences group published early results from a genome-mining campaign run almost entirely by Claude agents. Given one high-level prompt — search a large DNA sequence database for interesting new reverse transcriptases (RTs, enzymes that copy RNA into DNA) — roughly 950 agents spent 21 hours and 210 million tokens combing the data. They collected over 200,000 RTs, isolated 3,500 candidate systems, and narrowed those to the 20 most compelling, each with a human-readable report proposing a function and the evidence behind it. Anthropic says the equivalent analysis takes an expert scientist weeks to months.

The hit came from one agent reading the raw DNA beside an unusual RT. Its log entry reads: "[The DNA next to the RT] is spectacular: I can see by eye a tandem repeat array … that's a CRISPR-like … repeat array?!" It then behaved like a human scientist: counting the repeats, measuring their spacing, comparing the layout against known RT systems, and searching the literature for a prior report before filing the candidate for human review.

The system is now called ART — array-associated reverse transcriptases. Found mainly in bacteriophages, it has three parts: the RT itself, a partner gene beside it, and a long array of evenly spaced DNA repeats. The underlying RT was already known from earlier work on a jumbo phage; its two defining features — the non-coding repeat array and an accessory protein of unknown function — had not been noticed together. Anthropic has released a pre-print, and lab work shows the array is expressed as distinct short RNAs, a hint of CRISPR-like programmability. The system's function is still unknown.

Feng Zhang, the MIT and Broad Institute professor who co-developed CRISPR genome editing, reviewed the pre-print and called the identification of RNA-repeat arrays associated with reverse transcriptases "genuinely intriguing," and hoped it encourages more scientists to explore AI-assisted research.

## Why It Matters

The headline number is agentic scale, but the method is the real story. Anthropic's workflow treats a protein family as a survey target: Claude reads the literature and reproduces established results to validate its methods, then hunts for family members or genomic neighbors that fit no known system. Every surviving candidate gets a written report; follow-up passes attack the evidence, and most candidates die there. A campaign may end with one candidate worth testing, or with none.

That elimination stage is the interesting engineering problem. With hundreds to thousands of candidate reports per campaign, the hypotheses themselves became an object of study: what separates a proposal worth a wet-lab experiment from one worth discarding? Anthropic feeds those answers back into the agents' instructions so they mimic the team's scientific taste — an attempt to encode tacit expert judgment as an evaluation signal.

Genome mining has always required someone to notice the unusual thing; restriction enzymes, Taq polymerase and CRISPR itself all began that way, and each became foundational. If agents can now run the survey and rank their own candidate lists, the scarce resource shifts downstream: to bench capacity, to verification, and to deciding which of thousands of plausible hypotheses deserves scarce lab time. All ART lab work was done by human scientists at BSL-1 and BSL-2 — the agents generate hypotheses, not wet-lab results.

## Impact

For biotech, ART joins a short list of systems that combine a reverse transcriptase with a repeat array — a family whose known members are programmable and perform cutting, copying and pasting of DNA. If ART proves programmable, it is a potential editing tool rather than a paper. That is a large if: the pre-print is an early report, not a functional characterization.

For AI engineering, the transferable lesson is the harness, not the model — parallel sessions coordinated by a custom orchestrator, agents drafting their own candidate reports, and a review loop that converts expert rejections into instructions. That pattern fits any domain with a large search space and cheap textual ground truth.

Several questions stay open. Scale is one: 210 million tokens produced 20 finalists, and the discovery rested on a pattern already visible in the data. Verification is another. Anthropic notes that most candidates are eliminated during critical re-analysis — exactly the stage where an agent's confidence is hardest to distinguish from a plausible story. The expensive part of science was never finding candidates. It was knowing which ones are real.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Anthropic — Claude discovers a novel enzyme system with CRISPR-like repeats](https://www.anthropic.com/news/claude-discovers-novel-enzyme-system) | HN Discussion: [488 points, 528 comments](https://news.ycombinator.com/item?id=49820134)*
