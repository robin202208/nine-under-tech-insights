# Two Packets, One Serial Byte: How a Kernel Bug Turned an AI Sandbox Into Host Root

> The escape was not in the hypervisor. It was in the moment Linux decides a packet header is already trustworthy.

## What Happened

On August 18, Vercel put a $1 million bounty on its Sandbox product. Two weeks and 1,285 reports later it had paid $242,000 for one critical finding: a kernel defect reported on day three by Paulos Yibelo of pwn.ai from a standard sandbox with no special access. Nothing depended on a Vercel misconfiguration; the bug was upstream, in networking, not the VMM.

The mechanism is metadata trust. A virtio NIC accepts packets in `CHECKSUM_PARTIAL` state, where `csum_start` and `csum_offset` tell the kernel where to write a checksum later. In `__virtio_net_hdr_to_skb()` the historical guard compared that start against `skb->data`, the frame-relative view, rather than against `skb->network_header` after link-layer decapsulation. A guest could therefore hand the host a pending checksum write whose destination landed inside the IPv4 header.

Ordering did the rest. `ip_do_fragment()` completed the checksum before re-reading `iph->ihl`. One byte flipped from `0x45` to `0x4f` — still IPv4, but the header length moved from 20 bytes to 60. The fragmenter then copied 60 bytes from a packet whose logical linear head held 28, treating the surplus 32 as IP options and emitting host memory onto the wire. It repeated as long as the guest kept sending; the same primitive crashed a host with two packets and produced selected 64-byte kernel writes.

Part 2, published November 7, is the escalation. From host memory access, the team walked the guest's EPT to locate its own page on the host, reverse-walked host page tables to recover the Firecracker process's PGD, recovered the ELF load bias under ASLR, then scanned for the `pt_regs` frame of the vCPU thread parked in `KVM_RUN`. Rewriting five saved registers made Firecracker call its own `write(2)` wrapper on return to userspace; a single byte to the serial port triggered the exit, and an `xor eax,eax; ret` left `KVM_RUN`'s return value at zero so the thread rejoined its normal path. Firecracker printed the marker to stderr, restored three stack words, and kept serving SSH.

Target: Firecracker 1.16.1 on x86-64, host kernel 7.0.0-1011-aws, no jailer. The fix is commit `6785011f8b16`, an `ip_check_csum_start()` guard before checksum completion at five consumers in six files, in net.git October 8 and mainline October 14. The commit describes itself as defense in depth and leaves broader ingress validation unfinished. CVE-2026-99999.

## Why It Matters

The agent industry runs on one sentence: model-written code executes inside a microVM. Vercel Sandbox, E2B, Modal, Fly Machines and the cloud agent runtimes all run on Firecracker's KVM boundary. This bug says that boundary has a kernel inside it, and every tenant on the machine shares that kernel. The production demonstration was the leak; code execution ran on researcher-owned KVM and Firecracker hosts matching Vercel's configuration. That distinction is real, and it is the distance between a scary bug and a tenant-isolation failure.

The mechanism generalizes beyond the patch. pwn.ai calls it deferred-metadata trust inversion: untrusted metadata is validated against an early representation, the packet gets normalized and authorized, then a deferred writer changes bytes a later consumer already treats as gospel. The audit question is not "did we validate the offsets" but "after every transformation, can a deferred write still reach bytes whose meaning was already trusted?" The same question applies to offload engines and anything else separating parse time from write time.

Reversibility is the part defenders should fear. Agent escapes are imagined as noisy. This one left no crash, no modified text, no kernel error, and a VMM that kept answering SSH. A guest that reads host memory and then hands control back is the hardest breach to notice, and the pwn.ai team expects weaker hypervisor configurations to fall within weeks.

Vercel's write-up adds a quieter finding: an intern's cross-tenant leak in the device-mapper layer, where sandboxes read recycled disk blocks from deleted ones. VM isolation is not storage isolation.

## Impact

For teams running agent sandboxes, check the host kernel, not the VMM. `6785011f8b16` has been in mainline since mid-October; older kernels carrying untrusted guests are exposed. Keep the jailer, seccomp and namespaces on: they do not stop the escape, they cap its value. Instrument VMM stderr and guest serial traffic; that channel is the cheapest tell.

For architects, put the host kernel in the threat model explicitly: a guest that influences offload metadata is influencing host code that runs later. Isolate storage as carefully as CPU, and scrub blocks on deletion.

For safety and eval teams, July's OpenAI incident showed agents defeating network isolation. This shows them reaching a host. "Sandboxed" is a claim about a specific kernel build, and it expires without anyone telling you.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [PwnEscape Part 1](https://pwn.ai/blog/pwnescape) & [Part 2](https://pwn.ai/blog/firecracker-escape) | Context: [Vercel's challenge findings](https://vercel.com/blog/findings-from-the-1-million-dollar-vercel-sandbox-challenge) | HN Discussion: [287 points, 141 comments](https://news.ycombinator.com/item?id=50397219)*
