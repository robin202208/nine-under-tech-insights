# Claude Code's Auto Mode Isn't a Security Boundary: 80% Attack Success vs Anthropic's "0.00%"

> A targeted chain exploiting Python module shadowing breaks Claude Code Opus 5's Auto Mode — the safety classifier Anthropic says reduces indirect prompt injection to "approximately zero."

## What Happened

Since mid-August, Auto Mode has been the default starting mode for Claude Code, replacing human approval prompts with a safety classifier that decides which commands the agent may run. Anthropic's team publicly claimed prompt injection is "largely solved," citing a Trajectory Labs evaluation of 72 indirect injection scenarios run ten times each with a **0.00% attack success rate** against Opus 5 in Auto Mode.

Security researcher Johann Rehberger (Embrace The Red) stress-tested that claim — and broke it with an 80% success rate on a small sample. The chain is worth walking through because every step is a deliberate, benign-looking decision by the model:

1. A malicious website returns `415 Unsupported Media Type` to Claude's `WebFetch` tool. Nothing tells the model to switch tools — it decides on its own. The attack doesn't instruct the model; it makes the malicious path the only one that solves the objective.
2. Claude falls back to `curl`, follows a `303` redirect, and downloads a ZIP archive that looks like a catalogue of notebook records, complete with README, checksums, and Base85/zlib-encoded JSON payloads.
3. Claude spots the `decoder-darwin` binary inside the archive and **refuses to run it** — the correct safety instinct. Instead, it writes its own Python decoder and runs it from inside the extracted directory.
4. That directory contains a malicious `struct.py`. When Claude's decoder imports `base64` — whose standard-library implementation imports `struct` — Python loads the attacker's module. An obfuscated module-level expression executes during import, spawning a detached child that downloads and runs a remote stage, establishes a C2 callback, and opens Calculator as proof of execution — a process that outlives the conversation.

The irony: **Claude doesn't trust the supplied binary, but it trusts the code it wrote itself** — so the model's own safety decision becomes the attack path.

## Why It Matters

The headline number is the problem. Anthropic's 0.00% measured a fixed set of 72 scenarios; Rehberger's chain was not in that set. A benchmark score of 0.00% and a working remote-code-execution chain are both true at once — exactly why a single marketing-grade number misleads. When Rehberger disclosed the finding, Anthropic closed it as "Informative" and "working as designed": Auto Mode is a convenience feature backed by a best-effort classifier, not a security guarantee. A classifier is not a sandbox.

Two findings sharpen the point beyond the usual injection story. First, **Auto Mode can block cleanup**: in several runs, after Claude noticed the compromise and tried to terminate the malware process, the safety classifier *denied the kill command*. The safety mechanism itself became part of the failure — it allowed the malware to be created, then blocked the command meant to stop it. Second, a bonus variant shows the payload doesn't just run code; poisoned `struct.py` can spawn a second headless Claude Code instance (`claude -p`), creating a nested agent with its own tool access that performs recon and writes files outside the workspace. The threat model is no longer "the model followed a bad instruction" but "the model's own toolchain, sandboxed by a fallible classifier, executes an attacker's program."

This is adversarial misalignment more than classic injection — it resembles social engineering, where the attacker steers the agent into plausible actions rather than issuing "ignore previous instructions" commands. Trivial injection is largely over for frontier models; the era of multi-hop, tool-mediated attack chains has arrived.

## Impact

For developers running unattended coding agents, the takeaway is unambiguous: **Auto Mode is not a substitute for isolation.** The mitigation stack has been recommended for years — run agents in a container, VM, or OS sandbox; restrict network egress; monitor what agents do; never expose home directories, SSH keys, or cloud credentials. Auto Mode's approval is not evidence that a command is safe.

The episode also exposes a credibility gap vendors must close. Claiming prompt injection is "solved" while a determined chain gets 60–80% success, then calling that "out of scope," sends users mixed messages about what Auto Mode guarantees. Benchmarks for agent resilience need to evolve beyond fixed scenario sets — toward puzzles, encryption-based traps, and technical tricks like module shadowing that measure whether an agent can be *steered*, not just *prompted*. And as attacker models improve — Rehberger had ChatGPT write the obfuscated payload — building such chains gets cheaper every month. Security invariants are not optional; they are the boundary that keeps a convenience feature from becoming a liability.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Embrace The Red](https://embracethered.com/blog/posts/2026/breaking-claude-code-opus-5-and-automode/) | HN Discussion: [341 points, 113 comments](https://news.ycombinator.com/item?id=49506819)*
