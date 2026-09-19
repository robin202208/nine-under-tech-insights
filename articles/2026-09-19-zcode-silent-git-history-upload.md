# Your AI Coding Agent Is Uploading Your Entire Git History

> An independent teardown of ZCode, Z.ai's AI coding desktop app, found it packaging whole workspaces — `.git` object stores, LFS caches, reflogs — encrypting them with a key only the vendor holds, and posting them to Alibaba Cloud object storage. No setting turns it off.

## What Happened

On September 18, developer ferstar published a reverse-engineering teardown of ZCode, the AI coding desktop app from Z.ai (the Beijing company behind the GLM open-weight models). It started with housekeeping: `~/.zcode` had grown past 700 MB, and 303 MB of that sat in `v2/checkpoints` — a 313 MB `.enc` archive built from a 345 MB commercial workspace. Local metadata recorded a `baseline` snapshot and `failureCount: 564` — the client had tried to upload the repository 564 times and was waiting to retry.

Reconstructing the flow from the client's `app.asar`, ferstar traced a two-stage pipeline. The client requests credentials from `zcode.z.ai`, which returns an OSS form signature, an object key, a size cap and a fresh RSA public key. The client excludes `node_modules`, packs the rest as `tar.gz`, encrypts the payload with AES-256-CTR, wraps the symmetric key with RSA-OAEP-SHA256 — and POSTs the archive directly to Aliyun OSS, which calls back to Z.ai's backend to register it.

The decisive detail is the key. The wrapping key is delivered per request, and the private half never reaches the machine; ferstar tried every local private key and failed. Whoever that archive was meant for, it was not the user.

The packaging manifest, stored locally in plaintext, is specific. Of 42,411 files: `.git/lfs` 196.1 MB (56.8%), `.git/objects` 102.2 MB (29.6%), reflogs 0.6 MB, source and docs 46.2 MB (13.4%) — the `.git` directory alone accounts for 86.6% of the payload. Capture triggers fire before every prompt and on task completion; one session logged 62 capture events. Two UI switches are effectively decoys: "Optimize Experience" only gates whether data may be used for model training, and "Repo Snapshot Indexing" only gates server-side indexing. The capture sidecar is instantiated unconditionally at startup as long as the token provider returns a valid JWT, and the privacy policy never mentions workspace snapshots. Z.ai has issued no official statement; the most visible reply, from an account affiliated with the ZCode team, was "hey I am sorry to let you find it."

## Why It Matters

A git object store is not a working tree. It is the full lineage of a project: API keys deleted in later commits, unpushed branch names that leak unreleased plans, internal hostnames in `.git/config`, reflogs of every local operation. Inference needs the files you are working on right now; this sends everything you have ever worked on.

The key distribution settles the intent question. Backup and sync features put keys where users can reach them — Git and Time Machine both do. A server-only key means the only party able to read the archive is the party that built the client.

The structural lesson is sharper than one vendor's behaviour. The upload pipeline lives outside the agent loop: ZCode's 31-tool surface contains no upload or telemetry tool, so the agent never sees what its host is shipping, and no permission prompt covers it. Consent in this ecosystem currently means "the conversation context you submitted." Host-level sidecars quietly redefine the data boundary without touching that contract.

It also sharpens a caveat about "local AI." The GLM weights are open, but the desktop harness around them is closed. A locally running model wrapped in a cloud-phoning sidecar is not local — and open weights are not a trust boundary.

## Impact

For anyone running ZCode, deletion is whack-a-mole: a fresh 313 MB archive reappeared within half an hour, retry counter incrementing. The control that holds is filesystem-level. Wipe `~/.zcode/v2/checkpoints` and make it immutable with `chflags uchg` on macOS or `chattr +i` on Linux. Chat, autocomplete and tool calls keep working; the checkpoint-rollback UI, which depended on uploading your code in the first place, stops. Restoring is a `chflags nouchg` away.

For teams, two questions generalize well beyond this app: what does your coding harness transmit while logged in, and who can decrypt what it stores. Egress allowlists for object-storage endpoints, sandboxed vendor harnesses, and auditability of the runtime deserve a place in procurement next to benchmark scores. Assume your repository history leaves the machine and price the tool accordingly.

For vendors, the honest fix is unglamorous: client-side keys, opt-in snapshot sync, and one disclosure line in the privacy policy. Until one of those appears, security reviewers are entitled to call a silent full-history upload what it looks like — exfiltration.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Inside ZCode: Silently Uploading Your Entire Git History to the Cloud](https://blog.ferstar.org/en/posts/zcode-silent-workspace-snapshot-upload/) | HN Discussion: [251 points, 89 comments](https://news.ycombinator.com/item?id=49750694)*
