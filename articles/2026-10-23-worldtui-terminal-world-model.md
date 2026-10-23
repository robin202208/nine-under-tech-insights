# A 360M Model That Pretends to Be a Terminal — and Remembers Your Files

> There is no shell behind it and no filesystem underneath. Every keypress goes in, a screen delta comes out, and the model must keep the world consistent alone.

## What Happened

On October 21, Simon Edwardsson (co-founder of V7) published **WorldTUI**, a 360M-parameter model fine-tuned from Qwen2.5-Coder-0.5B to *be* a terminal. No shell, no filesystem, no container at inference time: raw keypress bytes go in, and the model predicts which screen rows change and where the cursor lands. It opens `vim`, splits `tmux` panes, pages `man`, and keeps files between commands. Type `pythoon` and Python launches anyway; type `ython` and it doesn't.

The representation is the interesting part. A terminal is a character grid, not a token stream — `clear` wipes the screen and sends the cursor home, `esc` changes vim mode without printing. So a session opens with a grid snapshot, and each turn is a byte input answered with `ROW_n <col> <text>` updates drawn over the previous screen. The vocabulary is 333 tokens, with grammar-constrained decoding so every prediction is a legal screen update; a `COPY` op moves a row for three tokens instead of about 96.

Training data was generated, not scraped: real shells inside Docker, recording every keypress and its screen, with randomized prompts and terminal sizes, 35% of sessions starting mid-command. That produced 336k episodes, roughly 2B tokens. Plain supervised fine-tuning learned typing fast but compounded errors over long sessions, so he borrowed DAgger: let the model roll out a few events, diff its screen against the recording, and synthesize a repair, but only when the right characters were inferable from earlier real screens — otherwise the correction trains hallucination. A third of sessions yielded usable corrections; PresetBench went 24/47 to 38/47 at int4, for 75 GPU-hours on one RTX 4090.

The numbers are concrete. On ShellBench (106 commands, exact match) WorldTUI scores 80/106 against 52 for the prompted base model. On a memory test — 32 worlds writing two random values to two files, read back in a separate session — it gets 9/13 exact readbacks versus 2/13. Decoding rows in parallel, one batch element per row sharing a KV cache, cut large-frame p95 latency from 6.7s to 0.87s. Trimming the 151,936-token embedding table to 333 tokens shrank 494M parameters to 360M; int4 reached 208MB, served by a hand-written Qwen decoder in WebGPU shaders.

## Why It Matters

The author names the real question: can the model maintain a consistent world, or just generate convincing terminal output? That is the line between a world model and a text generator, and a terminal is an unusually good place to measure it: the target is discrete, exactly comparable, with no rendering ambiguity. Prompted models already know what `ls` prints (52/106), yet almost never carry a file across sessions (2/13). Consistency comes from training on state transitions, not from knowing Unix.

The most valuable finding hides in an abandoned experiment. Removing layer 7 from a 78/106 checkpoint, without retraining, moved next-token accuracy from 99.3% to 98.9% — and dropped memory readbacks from 64/64 to 0/64, with ground-truth history fed in, so the failure wasn't error compounding. A first 16-layer student picked the wrong final layer and returned the right value only 31/64 times — chance. A student keeping layers 1, 7, 15 and 22 passed 64/64 at 138MB, but fell to 65/106 on ShellBench. As the author puts it, a student can look fine and have quietly lost its memory. Next-token loss is blind to the property you care about.

The limits are concrete: after `echo green >> colors`, `cat colors` often omits the appended line; nested directories drift; the Python REPL produces GPT-3-era hallucinations.

## Impact

For agent builders, a learned environment is a sandbox with no side effects and no boot cost — cheap RL rollouts, deterministic replay, and a way to exercise agents against software that isn't installed. The discipline it implies: the real shell stays the oracle.

For evaluation, the lesson generalizes past terminals: score world state with exact match rather than text similarity, test persistence across sessions rather than within one, and validate compressed agents on state-holding tasks. A 0.4-point next-token drop was hiding a total loss of memory.

For deployment, 208MB in a browser tab through 4-bit WebGPU shaders is the realistic path for tiny world models — client-side, no cluster, no API bill. The next experiment is the one to watch: training on thousands of randomly generated applications to see whether the model can predict an app it has never encountered. If it can, the question becomes whether application semantics can be learned rather than implemented — and HN commenters are already probing the other end.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [SimEdw's Blog](https://simedw.com/2026/10/21/world-model-terminal/) | HN Discussion: [505 points, 156 comments](https://news.ycombinator.com/item?id=50190363)*
