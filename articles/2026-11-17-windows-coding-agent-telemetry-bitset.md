# Your OS Now Counts Which Coding Agent You Run — and Stores It as a Bit

> Windows 11 ships a component that fingerprints `codex.exe`, `claude.exe`, `cursor.exe` and friends, then folds the result into an "optional" diagnostic upload that is on by default.

## What Happened

Xusheng reverse-engineered `MissionControlAggregator.dll`, a component loaded by `AggregatorHost.exe`, a child process of `DiagTrack` (Connected User Experiences and Telemetry) that starts automatically on Windows 11. The DLL contains literal string comparisons for CLI coding agents — `copilot.exe`, `claude.exe`, `codex.exe`, `agy.exe`, plus extensionless variants for the WSL path — and for five desktop agents: `github.exe`, `claude.exe`, `codex.exe`, `cursor.exe`, `antigravity.exe`. It also matches a broader developer-tool set: `node.exe`, `python.exe`, `dotnet.exe`, `gcc.exe`, `clang.exe`, `gdb.exe`, `javac.exe`, `devenv.exe`, `code.exe`. Some matchers also check publisher strings such as Node.js Foundation and Microsoft Corporation; the compiler group has no such check.

The component does not scan processes. It subscribes to events Windows already emits: `KernelProcess.AppStateChangeSummary` and `Win32kTraceLogging.AppInteractivitySummary` for Win32 programs, and `Microsoft.Windows.Lxss.Manager.Exec` for WSL. A match sets a slot in internal state, which `ReportCohorts<12,4,5>` converts into bitsets and emits as an ETW event named `Segments.SubCohorts` under provider `Microsoft.Windows.MissionControlAggregator`. The numbers map to twelve tool categories, four CLI slots, and five desktop slots. The aggregator explicitly discards runtime, focus time, and resource counters; it keeps only the bitmap. State is persisted to `C:\ProgramData\Microsoft\Diagnosis\AggregatorStorage\MissionControlAggregator$`.

Then it ships. Inspecting `EventStore.db`, the author found the report in DiagTrack's upload queue with `"privacy": {"isRequired": false}` — optional diagnostic data — and confirmed that a clean Windows 11 Home ARM64 install with US settings ships with optional diagnostics enabled. A copy of `cmd.exe` renamed to `codex.exe` was sufficient to set CLI slot 2 (`1 << 2`); a `cmd.exe` rename to `gcc.exe` set developer-tool slot 3. The feature is gated behind `CLICodingAgentUsage` (feature ID 62580080) and was already enabled on the author's VM as a service experiment; the public ISO image ships it off, so it is not provably active everywhere.

## Why It Matters

AI adoption measurement just moved from surveys to the operating system. Until now, "how many developers use coding agents" was answered by vendor seat counts, self-reported surveys, or GitHub telemetry. Here it is a device-level cohort bit, collected by default, tied to a Microsoft product-experiment feature ID. That is a different class of data: not a crash dump, not a feature click, but an inventory of which AI vendors an individual runs on a specific machine, pitched into the same pipeline as ordinary diagnostics. The consent bucket is "optional diagnostic data" — language written for telemetry hygiene, not for reporting on AI tool usage in a workplace.

The measurement is also structurally weak. Identity resolution is executable-name equality. Renamed binaries register, wrappers and containers may not, an `ssh` session into a Linux box vanishes, and a daily-driver agent looks like a CI runner that launched once. The author's own `cmd.exe` trick is the proof: a two-second rename produces a "Codex user." Any downstream dashboard built on this signal inherits both false positives and a trivial gaming surface.

That second point makes this more than a privacy story. When adoption data becomes a cohort dimension that steers feature investment and partner negotiation, the ability to fake adoption becomes a business input. The same class of bug appeared in benchmark work all year: metrics that look objective, resolved by string matching, and therefore steerable by whoever can edit the string.

## Impact

For individual developers, the action is narrow: check **Settings → Privacy & security → Diagnostics & feedback → Send optional diagnostic data**; if it is on, your tool usage is in the upload queue. Assume any renaming choice you make — wrapper scripts, vendored binaries, CI images — is now part of someone's report, and that a name collision is enough to attribute it to you.

For enterprises, the harder problem is inventory. Organizations that spent 2026 building shadow-AI registers, DLP rules, and acceptable-use policies for coding agents now have an un-inventoried egress channel. Ask whether OS-level optional diagnostics is covered by the DPA, whether it needs disclosure to a works council, and whether endpoint baselines should ship with optional diagnostics off by default. Procurement and security questionnaires should add "does the OS report which AI tools execute" to the standard list.

For Microsoft and anyone shipping agent tooling, the signal is directional: AI tool usage is now a first-class Windows cohort rather than an application-level metric. Expect more of it, joined server-side to entitlement data — and expect that reverse engineers, increasingly pointed at binaries by AI agents, will keep finding the strings first. The lesson for vendors is not that telemetry is forbidden, but that a name-based matcher inside a privileged system component will be read as a claim of authority over identity, and will not survive scrutiny.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Xusheng — Windows 11 coding agent telemetry](https://xusheng.dev/posts/windows-coding-agent-telemetry/) | HN Discussion: [214 points, 101 comments](https://news.ycombinator.com/item?id=50524736)*
