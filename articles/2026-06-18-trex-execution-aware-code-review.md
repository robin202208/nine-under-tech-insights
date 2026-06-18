# AI Code Review Just Learned to Run Your Code — And That Changes Everything

## What Happened

Greptile, the AI code review platform, has released TREX (Test, Run, Execute) — an execution layer that moves AI code review beyond reading diffs into actually running the code. Unlike every AI reviewer before it, TREX spins up services, runs tests, takes screenshots, and surfaces runtime failures that no amount of static analysis could catch.

The team started by building TREX as a standalone agent that generated and ran tests independently. That failed. Generating tests was not the same activity as finding bugs, and the separate agent created noise without surfacing relevant issues. The obvious fix — merging it into a single agent — also failed: one agent handling the full review got overloaded between spinning up services, capturing screenshots, and running tests. Too much context for clean management.

The breakthrough was making TREX share context with the main reviewer rather than exist as a separate product. Unlike two independent agents, TREX inherits what the reviewer already discovered about the codebase. It does not start from scratch. This shared-context architecture means execution results feed back into the review, and the review guides what to execute — a tight loop that two separate agents could never achieve.

## Why It Matters

Static code review has a fundamental ceiling that the industry has largely accepted. An AI (or human) reviewer can reason about what code *says*, but it cannot tell you what code *does*. Logic errors that need a specific sequence of state, UI regressions that only appear after page load, race conditions requiring a real request — these bugs are invisible in a diff. In 1976, Michael Fagan introduced formal code inspection at IBM where developers would print out listings and read through them line by line. Nearly 50 years later, we are still fundamentally doing the same thing, just on screens with AI assistance.

TREX represents the first serious attempt to break through that ceiling in an automated way. By making code execution a first-class review primitive rather than an afterthought, it catches the entire category of bugs that exist only when the program is running. This matters enormously for real-world software quality: runtime bugs are disproportionately responsible for production incidents because they are the ones that slip past review.

The architecture is also instructive for the broader AI agent industry. Greptile learned the hard way that independent agents working on related tasks create waste — overlapping exploration, redundant compute, and missed connections. Their shared-context model, where sub-agents inherit state from a parent agent rather than starting cold, is a pattern worth studying for anyone building multi-agent systems.

## The Impact

**Short-term**: Teams using TREX will catch runtime bugs that would otherwise reach production. The immediate win is fewer incidents from the class of bugs that static review misses — logic errors, integration failures, and visual regressions.

**Medium-term**: This sets a new baseline expectation for AI code review. Just as “does it compile?” became table stakes, “does it actually run correctly?” will become the standard. Competitors will be forced to add execution capabilities, and the code review market will bifurcate between static-only and execution-capable tools.

**Long-term**: Execution-aware review is a step toward AI systems that truly understand software — not just as text but as living, running artifacts. The shared-context agent architecture Greptile developed may prove more important than TREX itself, influencing how the industry builds multi-agent developer tools for years to come.

---

*Published: 2026-06-18 | Source: [Greptile Blog](https://www.greptile.com/blog/trex-code-execution) | HN Discussion: [53 points, 48 comments](https://news.ycombinator.com/item?id=48571851)*
