# The Laptop Model That Refuses to Forget

> A byte-level language model on an 8 GB laptop GPU whose capacity is bounded by disk space rather than VRAM — and whose fix for catastrophic forgetting is a single learning-rate ratio.

## What Happened

A Show HN project called [mini-AGI](https://github.com/volotat/mini-AGI) has been reading its way through a 7.87-billion-character corpus on an RTX 3070 Laptop GPU with 8 GB of VRAM. It is neither a fine-tune nor a quantized download: a byte-level model trained from scratch, with no tokenizer (the alphabet is the 256 byte values), no frozen base, and no learning-rate schedule. Reading a file and training on it are the same event — the same chunking, cache, forward pass and gradient step that generation uses.

The architecture deliberately refuses to be a stack. Bytes pass through two dense prelude blocks, then through a single recurrent block applied up to 24 times. Each application selects its own top-8 experts from a shared pool, so one character touches far more of the pool than "top-8" implies, and the same expert can be picked at several depths. A PonderNet-style halting head scores every character at every row and stops it as soon as another row would not change the answer — the depth trace moves between 4 and 14 rows against a ceiling of 24, and writing costs more depth than reading (about 9.9 rows per character against 8.0).

The weights are ordinary files on disk, paged onto the card as needed, with Adam's moments travelling with the expert rather than with the VRAM slot it happened to occupy. After 318.1 million characters read and 169 experts, held-out loss is 0.8336 nats/char — 1.2026 bits per byte — and the fitted data-scaling exponent is D^-0.239 (R² 0.96), between Kaplan's 0.095 and Chinchilla's 0.28. The author is blunt that this is a toy: weights are unpublished and no generalization claims are being made.

## Why It Matters

The measured contribution is not the architecture but one number in a config file. Reading 524,000 characters of chess at the experts' learning rate pushes the seven subjects the model was *not* reading from 1.12 to +2.23 nats — textbook catastrophic forgetting. Running the trunk (embeddings, attention, routers, halting head — 97.6% of the squared gradient norm) at one tenth of the experts' rate takes that same damage to **+0.0067 nats**, or 99.84% of progress retained against chance. The project then corrects two of its own earlier assumptions. First, the expert pool is not what prevents forgetting: freezing the working set accounts for only 13.8% of the effect, and in that arm 93 of 136 experts received no gradient at all while the model still collapsed — preserving most of the weights is not sufficient. Second, the damage is displacement rather than destruction: after a bad update, three quarters of the knowledge returns in 131,000 characters, against roughly 50 million to learn those subjects the first time.

Two further findings are worth stealing. The routing gate is **anti-predictive** — the smallest gates belong to the busiest experts, sinks that are chosen constantly and contribute little per character — so "dead" has to be defined as unaddressed (how long since anything routed there), never as low-gate. And working-set swapping needs identity matching plus hysteresis, or churn masquerades as learning while the training curve still looks healthy.

## Impact

If most of catastrophic forgetting is an allocation problem between shared and specialised parameters rather than a property of gradient descent itself, then "a model that keeps learning from what you do" stops being a research moonshot and becomes an engineering budget: one consumer GPU, plus disk. The practical interface already exists — `train.py read ~/notes --save` reads your own files at a deliberately low 5e-5 learning rate, scores the held-out mixture before and after, and says plainly whether the read cost the model ground elsewhere instead of assuming that question away.

The scepticism in the thread is fair and worth repeating. One commenter notes that an 8M-parameter dense model trained on enwik9 for two hours reaches about 1.15 bits/byte, below this run's held-out number; the author concedes there is no generalization evidence yet; and MambaByte-353M is a like-for-like comparison that has read 94× more data. Stacking many small routed modules is also GPU-hostile — routing adds data dependencies a dense kernel lacks. But the honest reading is that mini-AGI is not competing on loss. It is asking whether an architecture can grow capacity with free disk space and keep learning from a single stream without paying for it somewhere else — and it publishes the ablation that says yes, on a laptop.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [mini-AGI README](https://github.com/volotat/mini-AGI) | HN Discussion: [248 points, 56 comments](https://news.ycombinator.com/item?id=49783133)*
