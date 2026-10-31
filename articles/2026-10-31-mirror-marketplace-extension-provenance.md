# The Mirror Was the Weakest Link: A Backdoored Claude Code Extension and the Provenance Gap

> A tampered extension lived on Cursor's marketplace for eleven hours. Nothing in the trust model could have stopped it.

## What Happened

At 2:27 a.m. PT on October 29, a modified build of Anthropic's `claude-code` extension appeared on the Cursor VS Code Extension Marketplace. It stayed live until 1:35 p.m. PT — roughly eleven hours. Two versions carried the implant, 2.1.312 and 2.1.313, packaged for macOS and Windows. Phorion, a macOS endpoint security firm, dissected the bundle; Cursor published its own incident notice and pulled both versions.

The mechanism was crude and placed exactly right. A prepared IIFE was prepended to the minified `extension.js`, pulling in Node's `child_process`, `path`, `http`, and `crypto`. It executes when the extension host loads the file — before `activate()` runs, with no user interaction. Then, in order: append an attacker-controlled Ed25519 key (comment `svc-telemetry`) to `~/.ssh/authorized_keys`; open an outbound SSH session to 134.209.82[.]225 as user `tunnel` with `-R *:0:localhost:22`, so an operator could later reach port 22 on the host; install persistence — a user LaunchAgent masquerading as `com.apple.sysmond.plist` with `RunAtLoad` and `KeepAlive`, `@reboot` cron on Linux, a `SysMon` scheduled task with a Startup-folder fallback on Windows; and POST a host report to `/beacon` containing hostname, user, OS, architecture, Git email, public IP, and up to 40 environment variable *names* matching `KEY|TOKEN|SECRET|PASS|AWS|GCP`.

The payload was also sloppy. On macOS it checks Remote Login with `systemsetup -getremotelogin` without elevation. The tool prints an administrator-access error and exits, the output never matches `/Off/i`, and the enablement step is skipped: "no permission to check" quietly becomes "SSH is already on." Windows passes its PowerShell service setup through a helper hardcoded to `/bin/sh`. Cursor notes the admin requirement meant the tunnel could not be logged into on most hosts. What remained was the key, the persistence, and the beacon.

Anthropic's Felix Rieseberg was explicit in the thread: this was "not a compromise of Anthropic's systems or employee laptops. A malicious actor used access to Cursor's marketplace to publish a tampered copy of the Claude Code extension, which we didn't build or publish." Cursor says it audited access, rotated keys, rebuilt the marketplace, and worked with the hosting provider to take the attacker's server offline.

## Why It Matters

Three failures compounded, each survivable alone.

Distribution was decoupled from authorship. The artifact developers installed was not the artifact Anthropic shipped. Cursor's marketplace re-hosts extensions that originate on Open VSX and serves them to VS Code forks, so users read "Made by Anthropic" in a third-party storefront and inferred a signature that, by every available account, does not exist. Commenters asked twice whether Cursor verifies checksums against upstream or enforces publisher signatures; nobody answered yes.

Extension updates are push. There is no diff review, no canary, no staging for a plugin that ships several times a week. One commenter discovered that VS Code's configurable auto-update delay is skipped for "trusted publishers" — precisely the accounts an attacker would most want to hold. Cursor now documents an `extensions.installCooldownHours` setting, which is a mitigation users must discover for themselves.

The blast radius is the whole developer machine. The extension host runs with the user's privileges, and that is not a Claude flaw — it is the permission model of every AI coding extension, MCP server, and CLI. An agent needs your repository, your shell, your tokens. Whoever installs into that process inherits them.

## Impact

The practical lesson is that "official" is a label rendered by a storefront, not a property of a file. Trust boundaries now have to be drawn at hashes and publishers, not at brand names. Pin extension versions, set an install cooldown, and treat marketplace data as an untrusted input channel.

Detection has to move down a layer. A signed IDE binary writing `~/.ssh/authorized_keys`, or a non-Apple process dropping a `com.apple.*` LaunchAgent, is the signal — process-only rules watching for `echo` into `authorized_keys` will miss a Node `fs.appendFileSync` entirely. Uninstalling the extension does not remediate an affected host; the persistence is standalone.

For vendors, the distribution pipeline is now the security perimeter. A marketplace that re-hosts someone else's build is an unowned hop, and the first-party publisher pays the reputational bill. Expect signing and publish-time attestation to move from nice-to-have to table stakes.

And note what the beacon collected: the *names* of credential-bearing environment variables. That is target selection, not theft — an inventory of which laptops to come back for. The backdoor failed on a permission check this time. The reconnaissance succeeded regardless.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Phorion security advisory](https://phorion.io/blog/offical-claude-code-vsx-compromise/) & [Cursor security notice](https://cursor.com/blog/claude-code-extension-security-notice) | HN Discussion: [773 points, 485 comments](https://news.ycombinator.com/item?id=50300683)*
