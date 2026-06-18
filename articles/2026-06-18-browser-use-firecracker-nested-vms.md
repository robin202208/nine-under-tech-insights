# Browser-Use Runs Firecracker VMs Inside EC2 VMs — And Makes It Fast

Browser-Use just published a detailed engineering post-mortem on their cloud infrastructure rebuild. The headline numbers: browser sessions dropped from \/bin/bash.06 to \/bin/bash.02 per hour, and new sessions start in under one second. What makes this remarkable is how they achieved it: they run Firecracker microVMs inside regular EC2 instances — nested virtualization that, by conventional wisdom, should be slow. Instead, through careful systems engineering, they made it 3x cheaper and faster than their previous unikernel-based architecture.

## What Happened

Browser-Use previously ran cloud browsers on Unikraft unikernels, which were cheap when idle but failed catastrophically under traffic spikes — one load test brought down production for 45 minutes because scaling required manual engineer intervention. The team rebuilt on Firecracker, Amazon's lightweight VM manager originally built for Lambda and Fargate.

The unusual architectural choice: instead of running Firecracker on bare-metal (.metal) instances as intended, Browser-Use runs it on regular EC2. Every browser session is a VM inside AWS's own VM. This means page faults, memory operations, and I/O requests cross two hypervisor layers. The initial cold start from VM resume to CDP-ready browser took 9.8 seconds.

The team attacked this with multiple layers of optimization. First, they switched from 4KB to 2MB memory pages, reducing page faults from ~100,000 per resume to ~1,100 — a 91x drop. They implemented a custom userfaultfd handler that pre-loads the memory pages Chromium accesses first. They replaced HTTP polling for readiness detection with vsock-based log monitoring, cutting the ready-signal latency to under a millisecond. The VM cold start dropped from 9.8 seconds to 3.1 seconds, and eventually to under 400ms.

For CPU, they use a two-phase strategy: during Chromium's launch burst, vCPUs float unpinned across the host to spread load. Once the browser is ready, vCPUs pin to dedicated cores with real-time scheduling priority, preventing noisy-neighbor interference. A 1,000-browser stress test that was losing 17% of sessions dropped to zero losses after these changes.

On the stealth front, Browser-Use runs fully headless Chromium — no display server, no GPU, no compositor — yet achieves 81% block avoidance on their stealth benchmark, the highest of any provider. They achieve this through a patched Chromium fork that modifies automation fingerprints at the lowest level, plus a library of tens of thousands of real browser fingerprints.

## Why It Matters

This is a masterclass in making nested virtualization production-viable for latency-sensitive workloads. The conventional wisdom — "don't nest VMs, it is too slow" — turns out to be wrong when you apply enough systems engineering. The cost advantage of regular EC2 over bare metal (faster provisioning, lower idle cost) more than compensates for the virtualization overhead.

The architecture also demonstrates a clean separation of concerns: a custom control plane handles scaling decisions in real time (replacing CloudWatch's one-minute windows), stateless edge routers forward CDP WebSocket bytes, and each browser gets full VM isolation. If one browser is compromised or leaks memory, the blast radius is contained.

For the broader AI agent ecosystem, this matters because browser automation is becoming a critical infrastructure layer. As AI agents increasingly interact with the web — scraping, form-filling, e-commerce, testing — the cost and reliability of browser sessions directly impacts the economics of agent-based products. Browser-Use's \/bin/bash.02/hour price point, combined with sub-second startup, makes it economically viable to spin up thousands of concurrent agent browsers.

## The Impact

**Short-term**: Developers building on Browser-Use Cloud get faster, cheaper, more reliable browser sessions immediately. The 100% reliability in a 10,000-session stress test is a strong signal for production use.

**Medium-term**: The techniques described — 2MB huge pages for VM resume, userfaultfd pre-loading, vsock readiness signaling, two-phase CPU pinning — will be studied and replicated by other infrastructure teams. The post serves as an open-source playbook for nested VM optimization.

**Long-term**: Browser-Use's next target — snapshotting VMs after Chromium is already running, skipping the 545ms Chromium startup entirely — points toward a future where browser sessions are effectively instantaneous. If they can pull it off, the distinction between "starting a browser" and "using a browser" disappears, which would be transformative for latency-sensitive agent workflows like real-time web interaction and live customer support automation.

---

*Published: 2026-06-18 | Source: [Browser-Use Engineering Blog](https://browser-use.com/posts/firecracker-browser-infra) | HN Discussion: [197 points](https://news.ycombinator.com/item?id=46407024)*
