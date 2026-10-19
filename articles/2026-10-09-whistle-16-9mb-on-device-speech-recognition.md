# Whistle: Speech Recognition in 16.9 MB, Small Enough to Live in Your CPU Cache

> Cactus ships a seven-language speech model that runs on the CPU, needs no dependencies, and loads into the same engine as its text model — one 16.9 MB file from audio to tool calls.

## What Happened

On October 2, Cactus released Whistle, an open speech recognition model that weighs 16.9 MB and runs entirely on the CPU with no dependencies. It transcribes 16 kHz mono audio up to 30 seconds in one pass across seven languages — English, German, French, Spanish, Italian, Dutch and Polish — with automatic language detection. Beyond a transcript, it returns word-level timestamps and speech embeddings, one row per 80 ms frame from the encoder.

The architecture is a deliberate reuse of Cactus's existing text model, Needle. The front end frames audio into 80 log-mel bins (25 ms window, 10 ms hop); a convolutional stem of 128 channels compresses 3,000 frames down to 375, one per 80 ms, and everything downstream runs at that rate. The encoder is eight Simple Attention blocks with four mHC residual lanes and a Monarch–Hadamard MLP in place of the feed-forward network — the same blocks Needle uses. The decoder is eight "laddered" Simple Attention blocks at width 512, with 8 query heads to 2 KV heads and engram lookups over 18,432 slots at layers 3 and 7. The one speech-specific addition is a gated cross-attention per decoder layer that reads the encoder, whose projections are computed once per clip.

The benchmarks, measured over 86,174 utterances against Whisper base (145.3 MB) and Moonshine tiny v2 (41.9 MB), are the point. Whistle leads on LibriSpeech test-clean and test-other, SPGISpeech, Earnings-22 and the FLEURS average; Whisper base leads on TED-LIUM, AMI and the MLS average. On an Apple M4 Pro CPU, time to first token is 11.1 ms versus 73.2 ms and 22.8 ms, and decode runs at 1,319 tokens per second against 266 and 262.

## Why It Matters

The headline number is not the word error rate — it is 16.9 MB. At that size a speech model fits in L3 cache on many CPUs, and when a model fits in cache instead of DRAM, the latency of servicing a request can drop by an order of magnitude. That changes which products are possible: always-on transcription in wearables, robots, dashboards and microcontrollers where a GPU and a network round-trip were never on the table.

Just as important is that Whistle shares the engine, the container format (`.cact`) and the quantisation of Cactus's text model. A single binary can hold both, so `needle_complete` takes an audio clip directly, transcribes it, matches the transcript against a tool schema, and returns one JSON object with the function calls plus the `audio_*` fields. The engine ships prebuilt for seventeen targets, from macOS and Linux through Android, iOS, watchOS, Windows on ARM, RISC-V, MIPS, the browser and WASI. Audio never leaves the device.

The laddered decoder is the quiet engineering trick. Every depth from two layers up was trained as a model of its own, so `--audio-depth` selects a shallower decoder at load time for cheap hardware without touching the encoder, which always runs all eight blocks.

## Impact

For developers, this is a template for shipping speech locally: one small file, no cloud, no GPU, a stable C API. But the HN thread — 529 points and 119 comments — was candid about the tradeoffs. Whistle has no streaming mode: the encoder is non-causal and the model decodes whole clips, a real gap for live dictation. Commenters reported quality holding up in English, even with a non-native accent, but degrading on Polish and messy, out-of-distribution audio; several saw the classic hallucination of "Thank you." on silence — a fingerprint of the YouTube-caption training data. The field is crowded: Parakeet v2 and the Qwen ASR family were repeatedly cited as faster or more accurate, especially for English.

The more interesting signal came from one commenter who wired Whistle into an Echo Show replacement: raw transcription recognised only 70 of 170 utterances, but constraining the output to a fixed grammar lifted it to 164 — a return, as another noted, to the grammar-restricted speech recognition of a decade ago. That is the real lesson. A 16.9 MB model is not trying to be the most accurate transcriber in the world. It is trying to be small enough to embed, cheap enough to run always, and good enough — with a little structure around it — to turn a voice into an action on a device that costs a few dollars.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Cactus — Whistle: Speech to Text in 16.9 MB](https://cactuscompute.com/blog/whistle) | HN Discussion: [529 points, 119 comments](https://news.ycombinator.com/item?id=50008427)*
