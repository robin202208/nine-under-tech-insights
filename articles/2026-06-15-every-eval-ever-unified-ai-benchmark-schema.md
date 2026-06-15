# Someone Just Built a Rosetta Stone for AI Benchmarks — And It Has 22,235 Models

**Date:** 2026-06-15  
**Category:** AI Evaluation / Infrastructure  
**Source:** arXiv 2606.14516 (Every Eval Ever)

---

## What Happened

A team of 38 researchers has released Every Eval Ever, the first shared schema and community-crowdsourced repository for AI evaluation results. The numbers are staggering: 22,235 models, 2,273 unique benchmarks, and 31 evaluation formats ingested into a single unified JSON document, hosted on Hugging Face.

The problem they solved is mundane but monumental. AI evaluation results are scattered across leaderboards, papers, blog posts, evaluation harness logs, and custom repositories — each in its own format. Different evaluation frameworks produce divergent scores for nominally identical evaluations and record metadata inconsistently. Comparing two models across benchmarks often requires manual data entry, custom scraping scripts, and educated guesswork about which version of which benchmark produced which number.

Every Eval Ever proposes a single community-governed metadata schema with a companion instance-level schema. It is source-agnostic by design, ingesting results from evaluation harnesses and papers alike, and optionally stores per-instance outputs for fine-grained analysis. Automatic converters translate results from popular formats, harnesses, and leaderboards into the unified representation.

## Why It Matters

The fragmentation of AI evaluation data isn't just an inconvenience — it's a scientific liability. When two papers report scores on "the same benchmark," they may have used different prompts, different few-shot configurations, different temperature settings, or even different versions of the benchmark itself. The scores are incomparable, but the leaderboards treat them as equivalent.

This creates three pathologies. First, model developers cherry-pick the most favorable evaluation framework for their results, knowing that readers rarely check which harness produced which number. Second, independent auditors cannot reproduce results without replicating the exact evaluation setup, which is often undocumented. Third, the field cannot perform meta-analysis — answering questions like "have reasoning benchmarks saturated?" or "does scale predict coding performance?" requires crawling, normalizing, and deduplicating results from dozens of incompatible sources.

Every Eval Ever attacks all three. The unified schema encodes evaluation metadata at a granularity that makes reproduction tractable: model identifier, benchmark version, prompt template, few-shot count, temperature, evaluation harness, and date. Automatic converters handle the ingestion pipeline so that existing leaderboards and harnesses can feed into the repository without manual intervention. The Hugging Face hosting means results are version-controlled, downloadable, and queryable.

The 31-format ingestion pipeline is particularly significant. It covers major evaluation harnesses — lm-evaluation-harness, HELM, BIG-bench, Open LLM Leaderboard — as well as custom paper formats, blog post snapshots, and model card metadata. This breadth means the repository isn't just a database of known results; it's a tool for discovering results that were previously invisible because they lived in formats that standard tooling couldn't read.

## The Numbers

The initial repository contains results from 22,235 models across 2,273 benchmarks. For perspective: the Open LLM Leaderboard tracks roughly 3,000 models. HELM evaluates fewer than 200. Every Eval Ever's scale comes from automated ingestion rather than manual curation, and that's the point — manual curation doesn't scale to the pace of model releases.

Thirty-eight co-authors from institutions spanning academia, industry, and open-source communities signal that this is intended as shared infrastructure, not a single-group project. The community governance model matters: the schema is designed to evolve through proposals and consensus, not unilateral decisions by any single organization.

## The Bigger Picture

Every Eval Ever addresses a problem that gets worse the faster AI moves. The number of published models is growing exponentially. The number of benchmarks is growing even faster, as each capability area spawns specialized evaluations. Without a shared schema and automated ingestion, the evaluation ecosystem fragments into a thousand incompatible islands — and model comparison becomes a dark art practiced by consultants and in-house teams with custom tooling that nobody else can audit.

The proposal's scope is ambitious but focused. It doesn't try to decide which benchmarks matter or which evaluation frameworks are correct. It only standardizes the representation of results, leaving questions of quality and methodology to the community. That restraint makes it more likely to succeed: it solves a coordination problem rather than imposing a normative standard.

The question is whether the major evaluation harnesses and leaderboards adopt it. Schema standards live or die on network effects. If lm-evaluation-harness and HELM ship native exporters for the Every Eval Ever format, the repository becomes the default source of truth for model evaluation. If they don't, it joins the graveyard of well-designed but under-adopted infrastructure. The 38-author coalition and Hugging Face backing suggest this one has better odds than most.

---

*arXiv: [Every Eval Ever: A Unifying Schema and Community Repository for AI Evaluation Results](https://arxiv.org/abs/2606.14516)*
