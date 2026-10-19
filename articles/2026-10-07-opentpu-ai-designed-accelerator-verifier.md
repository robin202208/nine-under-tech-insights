# Can Agents Build the Chip That Runs Their Own Inference?

> An open-source FPGA accelerator whose design was tuned by a ten-hour autonomous loop — and whose real lesson is that the verifier, not the loop, is the moat.

## What Happened

This week, a project called **openTPU** surfaced on Hacker News: an open-source AI accelerator, hardware and host software together, that runs modern models on real silicon. The subtitle is the hook — "developed by AI." The card is an Inspur YPCB-00338 with a Xilinx Kintex-7 (xc7k480t) FPGA and two DDR3 channels. On it, ten models — LFM2.5-230M, Qwen3-0.6B, Qwen3.5-0.8B/2B/4B, Gemma 4 E2B/E4B, SmolLM3-3B, Phi-4-mini — emit tokens **bit-for-bit identical** to a reference simulator. LFM2.5-230M decodes at 59–86 tok/s in 4-bit, and decode reads 82–94% of the DRAM peak: the design is openly bandwidth-bound.

The stack is small enough to read end to end — a custom ISA (eight 32-bit words per instruction), a kernel language (`ol`) and compiler, a bit-exact ISA simulator that serves as the spec, SystemVerilog RTL checked against it, and host tools (`otpu-chat`, `otpu-smi`, `otpu-lens`). Mixture-of-experts models larger than the card's 4 GiB stream experts from host storage at link rate.

The "AI" part is a companion project, **auto-arch-tournament**. It is an autonomous research loop, inspired by Karpathy's autoresearch, pointed at hardware. Each round, an agent proposes a microarchitectural hypothesis as schema-checked YAML; an implementation agent edits `rtl/` inside an isolated git worktree; then an evaluation gate runs **riscv-formal** (53 symbolic checks), **Verilator cosim** (byte-identical RVFI against a Python ISS), **3-seed place-and-route**, and CoreMark CRC validation. Only hypotheses that beat the reigning champion are merged. Across 73 hypotheses and 9h51m, the loop lifted a textbook RV32IM core from 2.23 to 2.91 CoreMark/MHz — **+92%** — using 40% fewer LUTs.

## Why It Matters

Sixty-three of those 73 hypotheses were wrong. Ten were accepted; fifty regressed, nine broke formal or cosim checks, four failed placement. One idea — moving DIV/REM off the single-cycle ALU path — first arrived broken and was caught by cosim before reaching the FPGA; two hours later a fixed rephrasing became the breakthrough win. Two proposals tried to write outside the allowlisted `rtl/**` path and were rejected before any evaluation ran. A late "improvement" that collapsed fitness by 73% was killed by comparison against the baseline.

That is the real finding. The loop — model, prompt, tools, scoreboard, parallel slots — is converging across the industry toward zero margin. What is not commodity is the verifier: the formal properties, differential tests, path sandbox, and CRC re-validation that encode what the domain means by *correct*. The project's own conclusion is blunt: "The next wave of companies is not going to be people writing code. It's going to be people writing verifiers, with a loop running against them." If you can write the rules down, an agent satisfies them faster than your team. If the rules live in three engineers' heads and a stale wiki page, the agent satisfies the rules it inferred instead — and you will not notice until production.

The result also reframes the "recursive self-improvement" headline. The project is candid that decode is DRAM-bound and an FPGA is nowhere near a datacenter accelerator, and skeptics on Hacker News correctly noted that a human wrote the harness the agent optimizes against. The testable question it poses is narrower: can agents build the hardware that runs their own inference? So far, the honest answer is a qualified yes — partially, with a hand-written verifier holding the leash.

## Impact

For developers, the transferable lesson is not "use an agent loop" but "harden the gate first." Verifier-gated design is already viable wherever correctness is writable: an ISA and formal suite for a CPU, ledger invariants for a billing pipeline, differential tests for a compiler, a regulator-signed property for a clinical workflow. Where you cannot write the rules, an autonomous loop is a confident guess generator.

For the hardware world, openTPU is a readable, Apache-2.0, full-stack counterpoint to closed accelerator IP — and a reminder that architectural and physical wins compound: the loop's gains came roughly 13% from instructions-per-cycle, the rest from a smaller design clocking faster. For agent research, it is a clean case study in what these loops actually deliver: many confident wrong turns, a handful of genuine wins, and a verifier doing the unglamorous work of telling them apart.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [openTPU](https://github.com/FeSens/openTPU) & [auto-arch-tournament](https://github.com/FeSens/auto-arch-tournament) | HN Discussion: [232 points, 295 comments](https://news.ycombinator.com/item?id=49980715)*
