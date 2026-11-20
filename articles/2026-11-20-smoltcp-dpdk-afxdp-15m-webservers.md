# 3,300 Bytes Per Socket: The Kernel Struct That Caps You at 2.5 Million Servers

> One engineer tried to run 15 million web servers on a single 10GB VM. The ceiling was never HTTP or CPU — it was four structs inside the Linux kernel.

## What Happened

On November 18, kapteko published a deliberately absurd benchmark: 15 million web servers on one VM with 10GB of RAM and one core. The premise is a joke; the engineering is not.

The baseline is C doing what every HTTP stack reduces to — `socket`, `listen`, `accept`, `recv`, `send`, `close` — at about 65,000 requests per second. Scaling up hits three walls in order.

Ports go first. A port is a `u16` and `ip_local_port_range` defaults to 32768–60999, so `listen()` fails near 28,000 processes. The fix is AnyIP: route a whole subnet to loopback and give each server its own address. A /64 is free; 15 million IPv4 addresses would cost around $250M.

Process memory goes second. At 1.5MB each, 15 million servers need 20TB. The fix is a custom runtime with a userland scheduler and LFI sandboxing: load every guest into one address space and intercept its syscalls. Saved state per guest is 16 bytes — a program counter and two file descriptors — 220MB for the fleet.

Even an idle, listening socket costs roughly 3,300 bytes, because `socket()` allocates `struct tcp_sock` (2,240B), `struct socket_alloc` (768B), `struct file` (184B) and `struct dentry` (192B) — 3,384 bytes whether the connection is active or doing nothing. Fifteen million of those is 50GB. The stock kernel runs out of memory around 2.5 million listeners, with throughput degrading well before that.

The answer is to skip the kernel stack. smoltcp is a standalone, event-driven TCP/IP implementation built for bare metal that needs no heap allocation. The device layer can be TUN (layer 3), TAP (layer 2), DPDK (full bypass through memory-mapped buffers), or the newer AF_XDP, which maps packets zero-copy into a userspace buffer while the kernel keeps the device. With smoltcp, memory per idle server falls to 620 bytes and about a kilobyte per active one — 16 million servers on the same VM. DPDK is fastest; AF_XDP sits just behind.

## Why It Matters

The number to keep is 3,384: the kernel's per-object tax on concurrency, priced when RAM was scarce and sockets were few. An idle listener needs no congestion control, retransmit queues, timers, VFS dentry or inode — only an address and a wakeup.

Once you account in bytes per connection, the concurrency discussion changes shape. One million active connections at 5KB of TCP state costs 5GB; sixteen million idle listeners at 620 bytes fit in roughly 10GB — about 5.4× denser, before the 1.5MB per-process overhead. Nobody optimised an HTTP server here. They changed which layer owned the state.

AI infrastructure is walking into the same wall. An agent fleet is a population of long-lived, mostly-idle network identities: each sandbox holds connections to a model API, a tool, or a queue, and sits silent between turns — the workload the kernel handles worst. The escape ladder in this post, from ports to processes to kernel socket structs, is the ladder agent platforms climb as concurrency moves from thousands to millions.

The tradeoffs are honest: DPDK busy-polls a full core, demands exclusive NIC ownership (the interface vanishes from `ifconfig`; enabling it over SSH cuts your own session), and interrupt storms become livelock. AF_XDP keeps the kernel in charge and loses little throughput — kernel bypass no longer requires trading away your operating system.

There is a quieter lesson. The author leaned on an LLM to learn the domain, then admits he believed idle connections cost zero memory — until `dmesg` disagreed.

## Impact

For infrastructure engineers, the actionable metric is memory per connection, not throughput per core. Any design assuming a socket is nearly free hits a wall between one and three million concurrent listeners — and it arrives as an OOM, not a clean error.

AF_XDP is now the pragmatic default when you need packet-level control: kernel keeps the device, eBPF decides what bypasses the stack, SSH survives, and the gap to DPDK is small. Reserve DPDK where 400Gbps line rate and 1.7ns-per-packet budgets genuinely bind.

For agent platforms the implication is structural. Giving every agent its own address and port becomes affordable at roughly 1KB instead of 3.3KB plus a process — turning per-agent network identity from a luxury into a default, and with it per-agent attribution, rate limiting and egress policy.

The caveat: this is a proof of concept, not a Linux runtime. Full POSIX compatibility is open-ended, and you inherit the debugging. Production reimplements only the syscalls your workload uses — and accepts that the kernel was doing more than the bill suggested.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [15 million web servers on one VM — smoltcp, DPDK, AF_XDP](https://kapteko.com/15-million-webservers/) | HN Discussion: [380 points](https://news.ycombinator.com/from?site=kapteko.com) | Code: [AI10M PoC](https://github.com/artemyarulin/AI10M/tree/main/poc/ten-million-webservers)*
