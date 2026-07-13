# Your AI Coding Agent Burns 33,000 Tokens Before It Even Reads Your Prompt

> A wire-level analysis reveals Claude Code sends 4.7× more tokens than OpenCode on every request — and the hidden multiplier is costing teams real money.

## What Happened

Systima, an AI governance startup, ran an experiment that every AI-coding team should replicate: they spliced a logging proxy between their coding harness and the model endpoint to capture exactly what gets sent on every request. The results are sobering.

On the exact same task — replying "OK" to a 22-character prompt — Claude Code shipped 33,000 tokens of system prompt, tool schemas, and injected scaffolding before the user's words ever reached the model. OpenCode sent 7,000. Same model (Claude Sonnet 4.5), same machine, same outcome. The gap narrowed to 3.3× on Claude Fable 5 because Anthropic's harness sends newer models a much smaller system prompt, but the asymmetry persists across model families.

Where the numbers get jarring is cache behavior. OpenCode's request prefix was byte-identical across every run — pay once, read forever at a tenth of the input price. Claude Code rewrote tens of thousands of prompt-cache tokens mid-session, run after run, and on the same task wrote up to 54× more cache tokens than OpenCode. Cache writes bill at a 1.25× premium.

Then the multipliers stack. A 72KB instruction file (AGENTS.md / CLAUDE.md) adds ~20,000 tokens to every single request. Five modest MCP servers add another 5,000–7,000. By the time a real production setup sends its first request, it is 75,000–85,000 tokens deep before the developer has typed a word.

## Why It Matters

This is not a "Claude Code is bad" story. Both harnesses completed every test task correctly. The multi-step task — a write-run-test-fix loop — actually converged input totals (Claude Code batched tool calls into 3 requests; OpenCode serialized into 9, re-paying its leaner baseline each time). The real insight is architectural: **request count multiplies baseline**.

Claude Code ships with 27 tools, including an entire background-agent orchestration suite (CronCreate, Monitor, the Task family, worktree management, push notifications). OpenCode ships 10. That 17-tool gap accounts for roughly 19,000 of the 26,000-token baseline difference. More capability, more overhead — a tradeoff that makes sense only if those extra tools consistently earn their keep.

The most damning finding is independent of quality: rewriting a byte-identical cache prefix mid-session buys zero code quality. It is the same content, paid for again at premium rates. An instruction file the harness silently ignores buys nothing either. Those are waste on any definition.

Subagents are the single largest multiplier. One small task that cost 121,000 tokens done directly cost 513,000 tokens when fanned out to two subagents — a 4.2× multiplier. Every subagent pays its own bootstrap, and its transcript then enters the parent's context. The HN thread is filled with developers who now explicitly forbid subagents in their CLAUDE.md after watching their quota evaporate.

## Impact

For developers, this demands a new discipline: **measure what your agent sends**. Systima's measurement rig is roughly 200 lines of Node. Point ANTHROPIC_BASE_URL at a logging proxy, run your daily tasks, and read the boundary. The token accounting falls out for free.

The operational takeaways are concrete. Check which filename your harness actually honors — Claude Code 2.1.207 silently ignored AGENTS.md and only ingested CLAUDE.md. Audit your MCP server schemas; a production email/calendar server ships schemas several times larger than the public test servers measured here. Configure subagent model selection — default subagents inherit the parent model, but many teams report that Haiku-tier subagents with Sonnet/Fable auditing deliver equivalent results at a fraction of the token cost.

For the industry, this benchmark exposes an uncomfortable asymmetry: AI labs that sell tokens also build the harnesses that consume them. When a single mid-session cache re-write bills 50,000 tokens for identical bytes, and when subagents multiply costs 4× by default, the incentive structure warrants scrutiny. OpenCode's open-source model and byte-identical caching show that a leaner path exists. The question is whether harness builders will walk it.

--- 

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Systima AI](https://systima.ai/blog/claude-code-vs-opencode-token-overhead) | HN Discussion: [454 points, 254 comments](https://news.ycombinator.com/item?id=48883275)*
