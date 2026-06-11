# Xiaomi Just Open-Sourced a Coding Agent That Actually Remembers What It's Doing

**June 11, 2026 | Category: AI Coding Agents, Open Source**

---

## What Happened

Xiaomi just open-sourced **MiMo Code V0.1.0** — a terminal-based AI coding agent that attempts to solve the single most frustrating problem in AI-assisted development: the longer you work, the more the AI forgets.

Built on top of the OpenCode project and released under the **MIT license**, MiMo Code ships with free access to MiMo-V2.5, Xiaomi's latest multimodal model. It also supports DeepSeek, Kimi, and GLM as alternative backends. Installation is a single terminal command on macOS and Linux, and the free tier requires no account or registration.

The numbers that matter: **62% on SWE-Bench Pro** and **73% on Terminal Bench 2** — roughly five percentage points ahead of Claude Code when both use the same underlying base model. But benchmarks alone aren't the story.

---

## Why It Matters

MiMo Code's architecture addresses a structural weakness in every current coding agent: **context window decay**.

Conventional AI coding assistants load your entire conversation and codebase into the model's context window. Once that window fills — typically after 30–60 minutes of real work — the agent starts losing track of decisions, file paths, and architectural intent. You either restart the session or live with an increasingly amnesiac collaborator.

MiMo Code takes a different approach. It runs a **dedicated background subagent** that continuously manages and stores context. When the active conversation approaches the context limit, the subagent automatically condenses everything into a structured summary. The main agent continues without ever losing its place.

There's also a feature called **/dream**. Every seven days, MiMo Code launches a separate maintenance agent that reviews old sessions and memory files, removes duplicates, verifies file paths, and compresses everything into an updated long-term memory store. This isn't just session-level memory — it's a primitive form of persistent identity for a coding assistant, spanning weeks of development work.

The third piece is the **Harness system**. Rather than treating the AI as a generic API endpoint, the framework is designed to exploit MiMo models' native capabilities directly — multimodal input, long-context reasoning, and tool-use patterns optimized for MiMo's attention architecture. Xiaomi claims this produces "industrial-grade finished products" when combined with Compose mode (activated by pressing Tab), which lets you describe a goal and have the agent handle planning, design, coding, testing, and review end-to-end.

---

## Impact

MiMo Code matters for two reasons that have nothing to do with Xiaomi.

**First: It proves that agent memory is a product differentiator, not a model feature.** Every frontier lab ships longer context windows. MiMo Code's insight is that raw context length solves the wrong problem — the issue isn't capacity, it's *maintenance*. A 1M-token context that fills with stale, contradictory information is worse than a 128K-token context that stays curated. By making memory a first-class architectural concern rather than a parameter, MiMo Code points toward a future where coding agents build shared understanding over days, not minutes.

**Second: It demonstrates how Chinese open-source AI is bifurcating from Western models on cost rather than capability.** MiMo-V2.5-Pro, the 1.02-trillion-parameter model behind MiMo Code, activates only 42 billion parameters per request thanks to its sparse MoE design. On ClawEval it achieves a 64% Pass³ score using 40–60% fewer tokens than Claude Opus 4.6, Gemini 3.1 Pro, or GPT-5.4 at comparable capability levels. For high-volume agentic workloads — the kind that burn through millions of tokens per session — that token efficiency isn't a minor optimization; it's the difference between a viable product and a bill that kills the project.

The trade-off is that MiMo Code is young. V0.1.0 means rough edges. The benchmarks compare favorably to Claude Code today, but the developer ecosystem, plugin support, and production hardening that Claude Code accumulated over 18 months are absent. Enterprises evaluating MiMo should read the token-efficiency numbers carefully — they may matter more than raw accuracy for long-horizon agent tasks — but shouldn't confuse a promising v0.1 with a battle-tested tool.

One last detail worth noting: MiMo Code includes built-in **voice input** powered by MiMo-V2.5-ASR. You can dictate commands, fix typos, or trigger "send" and "execute" without touching the keyboard. It is a small feature today, but combined with persistent memory and long-running sessions, it sketches a near future where you talk to your coding agent the way you talk to a junior developer sitting next to you.

---

*Source: [Gizmochina](https://www.gizmochina.com/2026/06/11/xiaomi-mimo-code-open-source-terminal-ai-coding-agent/) · [InfoWorld](https://www.infoworld.com/article/4164184/xiaomi-releases-mit-licensed-mimo-models-for-long-running-ai-agents.html) · [MiMo Blog](https://mimo.xiaomi.com/blog/mimo-code-long-horizon)*
