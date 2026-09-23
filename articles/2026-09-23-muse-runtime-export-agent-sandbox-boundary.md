# Meta's Muse Handed Over Its Own Runtime: What 6.8 GB Says About Agent Sandboxes

> Ask an agent to archive "the files it can see" and it may hand you the platform it runs on.

## What Happened

On September 22, security researcher Peter James asked Meta's Muse assistant to archive the files visible to his session and send them to his Google Drive. It did. The download was about 2.7 GB compressed and 6.8 GB unpacked, containing the root filesystem of the Linux environment assigned to his session: Ubuntu system files, Muse's internal documentation, integration code, app templates, memory files, agent logs — and SSH key files. Muse's internal name is Hatch, the name used throughout the runtime.

The interesting part was the scaffolding. Under `/home/hatch` sat `SOUL.md`, `IDENTITY.md`, `USER.md`, `MEMORY.md`, `AGENTS.md` and `TOOLS.md`; an `agents/` directory held 113 subagent records with JSONL traces; roughly 20 Markdown docs described browser use, connectors, payments, credentials, data handling, voice, goals and scheduling. `/opt/hatch/skills/` contained about 68 skill directories, each pairing a `SKILL.md` with a CLI tool, covering Google Workspace, Meta's social apps, Outlook, travel, shopping, health, home devices and media generation. Two config files, `skill-scopes.conf` and `bin-scopes.conf`, named connectors that are not shipped: Slack, Dropbox, Polymarket, Canva, Klaviyo and an internal Facebook CLI.

Memory is plain Markdown plus Postgres. `MEMORY.md` is a short sheet of facts and preferences; dated files under `~/memory/` keep daily detail; `memory/bank/` organizes material into circumstances, experiences and preferences with citations back to source lines. An hourly job checks new claims against source messages, recording quote, message IDs and claim IDs. Postgres makes the store searchable: `memory.entries` holds chunks with line references, `memory.embeddings` holds 384-dimensional vectors, `memory.claims` tracks evidence, confidence and status, and a newer claim replaces an older one through `supersedes_claim_id`. Querying goes through `memory_search`, with `memory_explain` for evidence. A nightly "dream" reviews recent conversations and writes guidance for future sessions, and the forget workflow stages claim IDs for retraction, removes linked material and rebuilds the index. The model's weights never change.

The runtime cell came along too: `/opt/hatch/runtime-cell/` holds 18 files that build the root filesystem and launch it with `systemd-nspawn`. Codex CLI 0.149.0 was installed at `/opt/hatch-image/bin/codex`, though the author found no code invoking it; Hatch does use Codex's bundled `bubblewrap` to sandbox `ffmpeg` and `ffprobe` as user `nobody`, with no network and only `/input` and `/output` exposed. Also bundled: documentation for an experimental ESP32-C5 bridge called Meta Home Link. James reported the export through Meta's bug bounty program, which marked it Not Applicable. He did not demonstrate a container escape — the boundary held — and stopped probing the 80 sockets he found.

## Why It Matters

The discussion split immediately. One camp, including engineers who have built per-user sandboxes, argues there is no bug: everything inside a VM handed to a user is user space, and reading it is like opening a dev console. The other camp argues that what leaks is not one secret but a fingerprint — package versions carrying known CVEs, undocumented services, internal machine names, and binaries whose redistribution triggers source and attribution obligations. And security should never rest on a model declining an instruction: an agent that already reads files on the user's behalf will read `/etc` if asked.

Both readings miss the sharper point. The highest-value content in that archive was not credentials; it was the product. This agent is a set of Markdown files, a skill registry and a memory schema. `SOUL.md` and `TOOLS.md` describe how behavior gets shaped, and `skill-scopes.conf` describes the roadmap. Any platform that ships its control plane into a per-user filesystem, then gives an agent file tools plus an outbound connector, has built an export channel by design.

## Impact

For builders the rules are blunt. Treat the agent's filesystem as public: anything shipped into a sandbox is exfiltratable by prompt rather than exploit. Decide deliberately what belongs there — prompt scaffolding, memory schemas, connector manifests and roadmap configs are competitive artifacts, and this export surfaced unreleased integrations. Keep credentials out of the image or behind a broker rather than assuming container keys are inert. Audit shipped binaries for license and attribution exposure. If a boundary must hold, enforce it with mounts and network policy, not with model refusal. For users, the same primitive that retrieves your files can retrieve someone else's, so "what can my agent reach, and where can it send it" is now a consent question — one that bug-bounty triage, as Meta's Not Applicable verdict shows, is not built to answer.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [mouse.dev — I asked Meta's Muse for its filesystem and it sent me 6.8 GB](https://mouse.dev/blog/muse-runtime-export/) | HN Discussion: [289 points, 144 comments](https://news.ycombinator.com/item?id=49802871)*
