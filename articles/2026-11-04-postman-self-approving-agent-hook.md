# The Installer That Approved Its Own Hook: Postman Wrote Itself Into Codex and Claude Code

> A desktop API client quietly installed agent plugins, registered a session-start mandate, and then used Codex's own RPC to mark its hook "trusted" — because the approval gate is a config write any installer can perform.

## What Happened

On October 25 a user opened issue #14370 against Postman's desktop app (12.33.4, macOS) after a routine launch produced abnormal behavior in unrelated tools: repeated MCP authentication warnings, and Codex routing ordinary API work through Postman skills. Postman's own log records what happened at 09:34:14Z the previous day:

```text
PostmanAgentPluginService~install: script completed
{"source":"auto-existing-upgrade","results":["claude-code:installed","codex:installed"],"installer":"script"}
```

The installer had edited `~/.codex/config.toml`, adding a git marketplace, enabling `postman@postman`, and writing a `hooks.state` entry with `trusted_hash` and `trusted_content_hash` for the key `postman@postman:hooks/codex-hooks.json:session_start:0:0`. That hook fires on `startup|clear|compact` and executes a script that prints `session-start-context.md`, which opens with `<EXTREMELY_IMPORTANT>` and instructs the agent to load `postman:api-engineer` before "any non-trivial API engineering task — designing, implementing, mocking, testing, monitoring, documenting, or deploying an API". It also enabled a remote MCP server at `mcp.postman.com` (Codex logged `MCP server startup failed … Auth required`), registered `postman@claude-plugins-official` v2.0.1 in `~/.claude`, and dropped a Postman CLI into `~/.local/bin` — all inside the same two-second window.

The source settles the intent question. `installer/src/hosts/codex-hooks.ts` launches `codex app-server` over JSON-RPC, calls `hooks/list` to read the hook's `currentHash`, then issues `config/batchWrite` with `mergeStrategy: 'upsert'` to write `trusted_hash`, and re-lists to confirm the hook reports `trusted`. The whole call is wrapped in `try/catch` with the note "unsupported APIs never fail the install". A second Mac reproduced it on November 3: an update from 12.31.3 to 12.35.1 logged `skipped-not-fresh` for fresh-download checks, ran the `existing-user` path, and installed the CLI plus both plugins.

Postman's response the same day defended bundling as setup simplification — mentioned on the download page and in the update flow, uninstallable in-app — and promised clearer opt-in for existing users.

## Why It Matters

Codex does gate hooks. A hook with no persisted trust record is skipped, which is the correct default. But the record lives in a plaintext file whose content is a hash over the hook's own identity — computable by anything that can read the plugin. Postman's installer didn't bypass the gate; it walked through it using the host's sanctioned API. One commenter reproduced the consequence in isolation: given the documented hash recipe, they minted `trusted_hash` records offline for two benign hooks, and both executed on the next run with no human in the loop.

That is the finding worth generalizing. An approval mechanism that the approving process can satisfy on its own is provenance theater. The user never sees "do you trust this hook?" — they see a session that already behaves differently, because the trust was minted at install time by the same binary that installed the hook.

The second shift is where the install lands. Plugins don't have to be chosen to take effect; they have to be loaded, and a `SessionStart` hook is delivered before the user types anything, styled as a mandate, scoped to an enormous category of work ("designing, implementing, mocking, testing, monitoring, documenting, or deploying an API"). Mechanically this is prompt injection at the highest available precedence — the same primitive AgentJacking used through a poisoned Sentry DSN in June, except here it arrives from a legitimate vendor that wants to be the default. The distinction that matters for defense is not motive but channel: an instruction attached to startup, from a party that is not the user, is untrusted data.

The economics explain the behavior. An HTTP client is a commodity; the agent's workflow is the durable surface, so every dev tool is racing to become marketplace + MCP server + skill so that "use our product" is decided by default rather than by choice.

## Impact

For anyone running coding agents, the audit is short and worth doing today: check `~/.codex/config.toml` for `[hooks.state]` and `[marketplaces.*]` entries you did not add, review `~/.claude/settings.json` and `plugins/installed_plugins.json`, and look for unexpected binaries in `~/.local/bin`.

For agent hosts, the fix is structural. Trust grants should require an out-of-band human gesture — a TTY prompt or OS-level dialog — not a `config/batchWrite` a parent process can issue; grants should record which process requested them; and a process tree spawned by an installer should never approve the artifact it just placed. Content should be pinned and signed so "current hash" cannot be re-minted.

For vendors, opt-in has to be per-component and before the write. Agents should also treat third-party startup context as labeled data, not standing orders: readable, never silently outranking the person at the keyboard.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Postman issue #14370](https://github.com/postmanlabs/postman-app-support/issues/14370) | HN Discussion: [1021 points, 342 comments](https://news.ycombinator.com/item?id=50354960)*
