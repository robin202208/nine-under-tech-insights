# Bun Just Opened a PR for Real Shared-Memory Threads in JavaScript

> After months of design and a 60k-line implementation, Bun's Jarred Sumner opened PR #249 on oven-sh/WebKit: shared-memory threads for JavaScriptCore. No more Worker postMessage ping-pong.

## What Happened

On June 20, Bun founder Jarred Sumner opened a pull request against oven-sh/WebKit implementing shared-memory threads for JavaScriptCore — the engine that powers Bun. The PR, weighing in at roughly 60,000 lines, introduces `new Thread(fn)` as a first-class concurrency primitive: a function that runs on another core, in the same heap, with the same objects. No structured clone, no message passing, no SharedArrayBuffer-only escape hatch.

The API surface is deliberately minimal. `new Thread((a, b) => expensive(a, b), x, y)` spawns a function call on another thread. `.join()` blocks and returns the result (or rethrows the exception). `await t.asyncJoin()` does the same without blocking. `Lock`, `Condition`, `ThreadLocal`, and `Atomics.*` on plain object properties round out the synchronization toolkit. Threads share one global object, one module graph, and one set of prototypes — `x instanceof Foo` is true on every thread because there's exactly one `Foo`.

The design is based on Filip Pizlo's 2017 WebKit blog post "Concurrent JavaScript: It Can Work!" The core insight: objects that never leave their owning thread pay zero concurrency overhead. When a second thread touches an object, the engine transitions it to segmented storage under a compare-and-swap. The JITs keep emitting today's fast-path code until the program actually shares — then a watchpoint fires and recompilation kicks in.

## Why It Matters

This is the most serious attempt to bring real shared-memory multithreading to JavaScript since Web Workers shipped in 2009. Workers give JavaScript a multi-process model: separate heaps, structured clone for data transfer, and SharedArrayBuffer as a byte-level escape hatch. That works for CPU-bound number crunching but falls apart for anything involving object graphs — parsers, bundlers, servers with shared caches, and any program where the working set isn't a flat array.

The PR body includes a brutal illustration of the status quo: the "state of the art" for running a function on another core involves stringifying your own source code, creating a blob URL, setting up an onmessage protocol, and praying the function doesn't close over anything important. In the threaded model, it's `new Thread(heavy, input).join()`.

The implementation strategy is two-phase. Phase 1 (complete) landed all the machinery under a global interpreter lock — 95 tests passing, TSAN-clean, serial regression under 0.5%. Phase 2 (in progress) removes the lock. The bring-up log is extraordinarily honest about what broke: stack-overflow checks routing to the wrong thread, exception state walking another thread's stack frames, and a show-stopping OSR exit bug where two threads spilled their register files into the same scratch buffer. The team ran a controlled experiment to prove a suspected memory corruption was fixed — 0/15 failures on the fixed path vs 4/15 on the pre-fix behavior.

Scalability measurements show JavaScript beating Java at 16 and 32 threads on a shared-nothing document indexing workload. At 32 threads, JS clocks 870ms vs Java's 1022ms — and 2.46x the zero-GC Go floor. The spec-exact workload (using idiomatic JS strings and Maps) is 13x slower, but 90% of that gap is traced to BigInt and string-map performance, not the threading machinery itself.

## Developer Impact

If this ships, JavaScript's concurrency story changes fundamentally. Thread pools over shared structures, fine-grained locking, condition-variable handshakes, lock-free counters — patterns that are standard in Java, Go, and C# — become directly expressible. A parallel map becomes eleven lines instead of a project. A shared cache becomes a `Map` with a `Lock`, not a hand-rolled hashmap on a byte buffer.

The cost model is carefully designed. Code that never shares pays ~0.45% serial overhead in the current measurements. Objects that do share pay a one-time transition cost plus ongoing indirection through segmented storage. The per-thread memory budget is 150KB-1MB busy and 30-50KB parked, versus roughly 5-15MB for a Worker.

The PR is explicitly marked experimental and "may never merge." The diff touches the object model, all four JIT tiers, the GC, and the VM lifecycle. Concurrent GC with threads active is implemented but stays off by default — it demonstrably overlaps execution with collection but doesn't yet move total wall time. The team is honest about what's not done: Windows, test262 coverage inside threads, and a full fuzzing campaign.

## Our Take

Bun's threading PR is the most technically ambitious JavaScript runtime change in years — not because threads are new, but because the design pays its serial cost upfront and proves it with measurements, not promises. Whether it merges or not, the ~60k-line diff and the brutally honest bring-up log are a masterclass in systems engineering that every runtime developer should read.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
