# EEBench Puts AI in the Analog Lab: Can Models Design Circuits That Actually Work?

> A new benchmark grades frontier models on real electronics design — SPICE simulations, component tolerances, and cost trade-offs — and the results suggest AI's hardware engineering era is just beginning.

## What Happened

On September 1, EEBench — a benchmark from the atopile team — published its first leaderboard measuring whether AI models can design working circuits. Claude Opus 5 scored 61.6% across 13 analog and digital design tasks, Grok 4.6 followed at 57.1%, and Claude Fable 5.1 landed at 56.4%. OpenAI's models lagged, with GPT-5.5 at 42.3% and GPT-5.6 Sol at 39.4%.

Instead of asking an agent to draw lines in a GUI CAD tool — which burns context on coordinates and menus — EEBench describes circuits in atopile, a declarative language where components, connections, and electrical constraints live in code. An agent can edit the design, build it, run a simulation, and inspect failures without leaving the project. Grading is fully deterministic: the harness compiles the submission, constructs the circuit graph and bill of materials, then runs ngspice simulations against named probes with hard upper and lower specification limits.

The tasks are deliberately messy, like real electronics. One public task is a residential energy meter whose processor must survive a 20 ms power dropout above its 3.0 V brownout threshold. Models immediately reach the "right" textbook answer — add a capacitor — but a real ceramic capacitor delivers far less than its advertised capacitance once voltage is across it, parts carry tolerances, and over-provisioning costs money and board space. A design that works at nominal values can fail with the parts that arrive. Harder tasks push every component to worst-case tolerance corners: an agent synthesizing a multiple-feedback low-pass filter must keep gain, cutoff frequency, and Q inside limits while selecting real manufacturer parts — with datasheet-derived specifications carried into the SPICE model — that can be ordered at reasonable prices. Technical score is combined with cost efficiency against a reference bill of materials; cost only counts once the circuit works.

## Why It Matters

EEBench represents a meaningful departure from how model capability is usually measured. Coding benchmarks hand an agent a compiler and tests; EEBench hands it a simulator and a spec, then grades voltages, transient responses, and behavior at component-tolerance corners. That is closer to giving an AI a physical engineering problem than almost anything else public — and it turns "looks plausible" into a measurable outcome.

The results also carry a signal about how models acquire engineering skill. xAI included EEBench in the Grok 4.6 model card, under "engineering acceleration," alongside 3D modeling and parametric CAD evaluations — its published run put Grok 4.6 at 60.0% with extended reasoning effort. Grok's position atop the leaderboard fits xAI's stated recipe: high-quality engineering data and reinforcement learning inside domain-specific environments, including computer-aided design. In other words, the same simulation harness that grades a model can become an RL reward signal during post-training. A failed run tells you exactly which voltage missed its limit, which operating corner broke, or whether the model solved the problem with an unnecessarily expensive design — far richer feedback than a human saying a schematic looks reasonable.

The timing is telling: OpenAI fronted its GPT-6 Astra launch with a KiCad circuit-board demo, and a frontier lab now cites an electronics benchmark in a model card. Both are early signs that labs are taking hardware design seriously as a target domain, not just software.

## Impact

For now, EEBench V1 covers design and verification through simulation — it does not yet judge layout, manufacturability, or full product bring-up. But even the current scope has crossed a threshold: for a useful and growing set of circuit problems, some frontier models genuinely can design working electronics.

The near-term consequence is pressure on the other frontier labs. EEBench's authors have not yet tested GPT-6 Astra — the model OpenAI demoed working in KiCad — and openly want to. With Grok 4.7 reportedly in training on SpaceX engineering data, electronics evals may become a recurring fixture of model launches, the way SWE-bench did for coding. For hardware teams, the practical implication is tooling: declarative design languages like atopile turn circuits into artifacts an agent can iterate on autonomously, a prerequisite for AI-assisted schematic design to move from demo to workflow. The honest caveat from the benchmark's own authors applies: nobody should yet ask a model to design a pacemaker and blindly manufacture the result. But the measurement problem they set out to solve — how do you know if AI-produced electronics are any good — now has a public, deterministic, and increasingly standard answer.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [EEBench](https://eebench.org/blog/can-ai-design-circuit-boards-yet/) | HN Discussion: [376 points, 206 comments](https://news.ycombinator.com/item?id=49569366)*
