# The Last 5× of Local AI Performance Lives in an Encrypted Firmware Table

> A dual-boot experiment on Nvidia's RTX Spark laptop shows identical silicon running roughly five times slower on Linux — not because of a kernel bug, but because a Windows-only power controller holds the key to the GPU's clock table.

## What Happened

On November 1, MindStudio published a hands-on report after dual-booting Linux on an MSI Prestige 16 Flip AI+, a laptop built around Nvidia's RTX Spark — the same SoC design as the DGX Spark desktop, shipped with Windows on ARM and up to 128 GB of unified memory. Linux installed and ran CUDA. Then the benchmarks landed: Meta's gpt-oss-120b generated 65.4 tokens per second on native Windows, 65.1 inside WSL, 61 on a DGX Spark running Nvidia's own Ubuntu build — and 13.9 on the laptop's native Linux install.

The gap traces to a device listed only in Windows Device Manager: "Nvidia Platform Controllers and Framework." In a laptop, the CPU and GPU share one power budget, and this controller arbitrates the split in real time. It has no Linux driver, and the firmware hands it a power-state table that is encrypted; the Windows driver stack can decrypt it, and Linux cannot. Disabling the controller inside Windows reproduced the exact failure: throughput fell from about 83 to 32 tokens per second and clocks dropped from roughly 2 GHz to 786 MHz — the same numbers Linux produces when plugged in. Re-enabling it restored about 78 tokens per second. Lifting the key out of the Windows driver failed, once triggering a kernel panic.

Two details make the story stranger. Unplugged, the same Linux install hit 61–67 tokens per second at about 1,800 MHz; plugging into wall power dropped it to roughly 15 tokens per second at 780 MHz, the opposite of normal laptop behavior, and die temperatures held at 40–47 °C, ruling out thermal throttling. Memory follows the same pattern: firmware reserved 96 GB of the 128 GB for the GPU, only Windows could change the split, and Linux saw just 28 GB until the reservation was cut to 4 GB.

## Why It Matters

The interesting variable is not the chip. It is the policy. Nvidia's own DGX Spark, on the same silicon, reaches 61 tokens per second on Linux, which rules out "Linux is bad at this GPU" as an explanation. What the laptop lacks is the per-OEM arbitration layer that turns a shared power envelope into clocks, and the table that encodes those decisions is both encrypted and platform-specific. An open kernel driver, in other words, is not an open machine: the driver can submit work, but it cannot decide how fast the work runs.

That reframes a measurement problem as a governance problem. A developer benchmarking gpt-oss-120b on this hardware can legitimately report 65 tokens per second or 14, depending on which OS booted, whether the charger is connected, and which OEM built the chassis. A five-fold spread in the denominator dwarfs most of the model-level throughput differences the local-AI community argues about weekly. Benchmark tables that omit OS, AC state, vendor SKU, and clock residency are now reporting a firmware configuration, not a capability.

Hacker News commenters pushed back: the DGX Spark works because Nvidia owns the whole stack and boots through standard UEFI/ACPI paths, and some Asus and Dell variants reportedly run Linux out of the box on the same Omarchy image that struggled on the MSI unit — a per-OEM devicetree gap, not necessarily a deliberate nerf. That nuance locates the failure precisely: consumer laptops increasingly replace platform standards with vendor-private controllers, and whatever those controllers encrypt becomes invisible to every operating system except the one that shipped first. Whether the motive is malice or launch-schedule triage, the mechanism is identical, and it hands the vendor a performance dial that no user can inspect.

## Impact

For buyers: Linux-first tooling such as vLLM or SGLang belongs under WSL today, which inherits the Windows power path and hit 127 tokens per second with eight concurrent clients versus 39.5 natively. For anyone benchmarking local models, publish the platform triple — OS, power source, OEM SKU — or the numbers are not comparable. For the ecosystem, the deeper ask is unglamorous: mainline per-SoC power-management drivers, and pressure on OEMs to express power policy through standard firmware interfaces instead of encrypted, Windows-only blobs.

The economic implication matters more. When a five-fold throughput swing lives behind a table only the vendor can read, performance segmentation stops being a silicon decision and becomes a licensing decision — the same knob that separates a workstation from a consumer part, now applied to whether your laptop can run a 120-billion-parameter model at conversational speed. Local AI was supposed to be the escape hatch from rented compute. It turns out the escape hatch has a clock table, and someone else holds the key.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [MindStudio — Linux on the Nvidia RTX Spark Laptop](https://www.mindstudio.ai/blog/linux-nvidia-rtx-spark-laptop) | HN Discussion: [590 points, 271 comments](https://news.ycombinator.com/item?id=50362905)*
