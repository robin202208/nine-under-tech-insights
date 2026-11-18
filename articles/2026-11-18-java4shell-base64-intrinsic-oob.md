# Sixty-Five Bytes to Root: The Base64 Tail Case That Broke Java's Memory-Safety Promise

> A 65-character string — 64 `A`s and one `=` — makes OpenJDK's vectorized Base64 decoder write 62 bytes past its own output buffer, on any x86-64 machine with AVX-512 VBMI.

## What Happened

On November 17, 2026, Calif — the group behind PHP2Shell — disclosed CVE-2026-114938, marketed as Java4Shell: a memory-corruption flaw in OpenJDK HotSpot's x86-64 Base64 decoder, scored 9.2 critical. Oracle patched it the same afternoon.

The bug is not in Java code. When a method decodes Base64 often enough, C2 compiles it to native code and HotSpot swaps in an AVX-512 VBMI intrinsic. For inputs under 64 bytes, that routine computes a store mask with `bzhiq(-1, output_len)` and writes with `evmovdqub`, committing every byte whose mask bit is set. Padding is handled by decrementing `output_len` once or twice for trailing `=` characters. Feed the decoder 64 `A`s and a single `=`: `output_len` reaches zero, the padding branch subtracts again, the value underflows to `-1`, `bzhiq(-1, -1)` leaves all 64 bits set, and a full 64-byte store executes. The decoder had allocated a 50-byte result array; the store starts at offset 48 and runs 62 bytes past the end, with bytes 48–63 always `0x9a`. Only then does Java reject the input — after the corruption.

Every OpenJDK build from 17.0.6 onward inherits the code: Corretto, Temurin, Zulu, Liberica, SapMachine, Oracle JDK, GraalVM. The gate is hardware: x86-64 with AVX-512 VBMI (Ice Lake Xeon and newer, AMD Zen 4+); ARM64 is unaffected.

## Why It Matters

The primitive looks useless: the write is fixed-value and only the landing offset is attacker-controlled. Quang Luong turned it into a general capability the way browser exploit developers have for a decade — overwrite a neighbouring array's length field. That array becomes a *writer*: with a corrupted length it accepts chosen bytes, converting a fixed spill into a bounded linear write. Corrupt a second array's length whose contents are echoed back in the HTTP response and you have a *reader*: heap disclosure — internally "JavaBleed" — leaking tokens, keys and PII before any code execution.

Placement is the hard part. HotSpot hands each thread a thread-local allocation buffer (TLAB), so requests on different threads advance different cursors, and G1 packs objects into region layouts the attacker cannot read. Calif forced allocations outside the TLAB with size and padding control, sprayed several writer/reader pairs to give the decoder multiple possible neighbours, and accepted failures — a miss crashes the JVM, but a crash costs nothing when the attacker can retry. Remote grooming meant sending partial HTTP bodies up to the critical allocation, waiting for a TCP acknowledgement, then completing the request.

The result: pre-auth execution in default configurations of Keycloak, Jira, Confluence, Artifactory, Jenkins and PingFederate; post-auth in Kafka, Solr, Elasticsearch, Gerrit and Spinnaker; plus a client-side variant where a Java program connecting over TLS to a malicious server is compromised. There is no universal payload — each target needs a profile — but the trigger is one line of text.

What makes this more than another CVE is economics. Luong found the bug in about an hour and had a working exploit three hours later for under $200 of tokens, by pointing Codex at HotSpot's JIT, intrinsics and cryptography instead of reading assembly himself; when the agent stalled during heap grooming, Calif gave it a heap tracer rather than more prompt. Discovery cost has collapsed. What stays expensive is target-specific engineering.

## Impact

Patch first: fixes shipped November 17, and the window on unpatched fleets is now days, not months. If you cannot patch, start the JVM with `-XX:-UseBASE64Intrinsics` or `-XX:UseAVX=2` on x86-64 hosts — the latter prevents startup on ARM.

Then audit reachability. Base64 sits in authentication, cookies, uploads and message decoding; ask whether any pre-auth path decodes attacker-supplied text. Runtime inventories matter more than framework versions — a patched application server on an unpatched JRE is still exposed, and container base images hide the difference. Detection is hard: the decoder still throws, so application logs look normal; the only signal is JVM crashes under malformed input, which most stacks treat as noise.

The wider lesson is architectural. Java's memory-safety guarantee is a claim about Java code, not the runtime, and the runtime now contains hand-written assembly that ordinary review rarely reaches. The flaw shipped because an optimization almost no application needed went to everyone — the commons paying for a feature someone else requested. Calif's proposed remedy — "vibe-uncoding" — is worth taking seriously: point agents at dead code and delete it. But the same agents will find the next one in an hour, so the real defensive budget is shifting from finding bugs to shrinking the gap between advisory and patch.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Calif — Java4Shell](https://calif.io/java4shell/) | HN Discussion: [514 points, 139 comments](https://news.ycombinator.com/item?id=50538240)*
