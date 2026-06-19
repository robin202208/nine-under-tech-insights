# Text-to-CAD Is Here: Adam Just Made Mechanical Engineering As Accessible As Coding

> YC W25 startup open-sources CADAM, an AI agent that turns plain English into 3D models — 4,474 GitHub stars in 24 hours.

## What Happened

Adam, a Y Combinator W25 startup, launched CADAM on June 18 — an open-source web application that converts natural language descriptions and reference images into production-ready 3D CAD models. The GitHub repository hit 4,474 stars within a day, signaling that mechanical engineers, product designers, and robotics builders have been waiting for exactly this.

CADAM operates on a deceptively simple paradigm: **text → code → CAD**. When you describe a part — “a 40mm servo bracket with four M3 mounting holes and a 5mm shaft collar” — the AI agent generates OpenSCAD code, which compiles to 3D geometry via WebAssembly in the browser. No desktop software. No license fees. No STEP file compatibility nightmares.

The stack is deliberately lightweight: React 19, Supabase for backend, and OpenSCAD compiled to WASM. Export formats include .STL (for 3D printing), .SCAD (for parametric editing), and .DXF (for laser cutting). Parametric sliders let you tweak dimensions after generation without touching code. The team — Zach Dive, Aaron Li, and Dylan Anderson — built it on two beliefs: AI will be the primary medium for mechanical design, and code generation is the right abstraction for CAD.

## Why It Matters

Mechanical CAD has been one of the last major engineering disciplines without AI-native tooling. Software development has GitHub Copilot, Cursor, and a dozen AI coding agents. Graphic design has generative fill and text-to-image. But mechanical design still runs on SolidWorks, Fusion 360, and CATIA — desktop software with learning curves measured in months and license costs measured in thousands. CADAM’s open-source, browser-based approach breaks both barriers simultaneously.

The code-as-intermediate-representation choice is the genius move. Rather than training a model to output geometry directly — which would require an impractically large dataset of parametric-solid-body pairs — Adam leverages LLMs’ proven strength in code generation and routes through OpenSCAD, a mature, deterministic compiler. Every output is editable, version-controllable, and diffable. A mechanical engineer can check the generated OpenSCAD the way a developer reviews AI-generated Python. This is not a toy demo; it’s a pattern that production engineers can actually trust.

The timing is also strategic. Desktop 3D printers now cost under $200. CNC routers are proliferating in makerspaces. The bottleneck has shifted from “how do I manufacture this” to “how do I design this.” CADAM removes that bottleneck.

## Developer Impact

If you build anything physical — robotics, enclosures, jigs, mounting brackets, drone frames — you now have an AI pair-programmer for mechanical design. Stop dimensioning mounting holes by hand. Describe the functional intent and let the agent handle the parametric geometry.

For AI application developers, CADAM demonstrates a reusable architecture: **LLM → domain-specific DSL → deterministic compiler**. This pattern applies anywhere a mature, text-based domain language exists: PCB layout (KiCAD files are S-expressions), FPGA design (Verilog), even legal contracts. The LLM generates the code; the compiler guarantees correctness. The human reviews the code, not the output.

Integration potential is significant. CADAM outputs industry-standard formats and is MIT-licensed (the CADAM app itself, while the core OpenSCAD engine is GPLv3). Expect CI/CD pipelines for hardware — “generate bracket, run FEA simulation, flag if safety factor < 2.0” — within months, not years.

## Our Take

AI-native tooling for physical engineering has been the missing piece in the current wave. Software ate the world; AI is eating software. Now AI-native CAD is coming for hardware. Adam’s open-source bet is the right one — trust in mechanical design comes from inspectability, and nothing is more inspectable than generated code you can read.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
