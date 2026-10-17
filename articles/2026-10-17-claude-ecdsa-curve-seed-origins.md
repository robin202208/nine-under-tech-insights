# The Seeds Were Just Timestamps: How Claude Ended a 25-Year Cryptography Mystery

> The NIST elliptic-curve seeds were never hashes of a secret passphrase — they came out of a 1990s C library PRNG seeded with the clock. The recovery script is the interesting part.

## What Happened

ECDSA is the most widely used elliptic-curve cryptography standard; NIST adopted it in 2000 as FIPS 186-2. For each curve the specification publishes a "seed" whose hash determines the curve's `b` coefficient — and those seeds arrived from the NSA with no explanation. P-256's seed is `C49D3608 86E70493 6A6678E1 139D26B7 819F7E90`, provenance unknown. That gap fed a 25-year controversy — Koblitz and Menezes's "A Riddle Wrapped in an Enigma", an amateur investigation, a cash bounty, and a persistent theory that the constants hide a deliberate backdoor.

In October 2026, an Anthropic employee asked an internal research model to work out how the seeds were generated, without feeding it the folklore hint. In under half an hour, Claude explained the seeds of P-192, P-224, P-256, P-384, P-521 and five ANSI X9.62 curves. None were hashes at all: they are outputs of `lrand48`, the C library's linear congruential generator, initialized with `srand48` from a Unix timestamp.

The method is textbook cryptanalysis with unusual stamina: Claude slid a scanner along each seed one bit at a time, testing across 34 generator/slice combinations whether adjacent chunks were consecutive output of a 1990s-vintage generator. It noticed that in four of the ten seeds the top bit of all twenty 32-bit words is zero — a pattern occurring by chance with probability 1/5810, the signature of `lrand48` returning only the top 31 bits of a 48-bit state. From there it brute-forced the 17 hidden bits, and for the truncated prime-curve seeds recovered the full PRNG state with the Lenstra–Lenstra–Lovász (LLL) lattice-reduction algorithm. Rolling each state backwards until the low 16 bits read `0x330E` recovered an `srand48` initialization: every prime-curve seed traces to a Unix timestamp between May 1997 and April 1998, the binary ones to dates mapping to April 17, 1998 when doubled. A second generator — the `zrandom` code in Arjen Lenstra's FreeLIP long-integer package, later shipped in early NTL — explains the base points of six curves, seeded with small integers 20000, 20420, 20507, 20509, 20512 and 20517. Anthropic published reproduction code (CC0) with a timestamp and candidate index per standard; the $36,864 bounty goes to the Internet Security Research Group.

## Why It Matters

The tempting headline is "the curves have no backdoor," and that is the correct conclusion — with careful boundaries. What was recovered is how the parameters were *generated*, not how they were *selected*. The write-up notes that the runs passed over viable candidates: for P-256, candidate 876 would also have produced a prime-order curve of the right size. In principle someone could have searched timestamps for a desired property, but that only pays off if you later disclose the timestamp as evidence of innocence — and nothing reveals how many runs there were. The honest summary is that the plausible mechanism for sabotage has evaporated, not that every imaginable selection criterion is excluded. One binary seed (B-163) and the base points of P-224, P-256, P-384 and P-521 remain unexplained.

The deeper significance is epistemic: a public artifact whose origin was unknowable for a quarter century becomes a computation anyone can rerun — with exact candidate indices, a public-domain script, and a claim falsifiable in minutes rather than reviewable in months. That is a materially different category from a paper-style AI claim. Note what the model did *not* do: it recognized a weak PRNG's fingerprint and applied decades-old lattice techniques — the novelty is the enumeration, not a new attack.

## Impact

"Provenance recovery" is about to become a standard audit task. Legacy standards, unexplained constants, test-vector seeds, undocumented datasets — anything whose generation process was never written down should be assumed reconstructible by an agentic model with tools and patience. Teams maintaining decade-old systems should treat provenance hygiene as security hygiene — and write down generation recipes while a human still remembers them.

The second-order effect is how we verify AI claims. The bounty went unpaid because the community had run out of ideas, not because the problem was hard; what changed was that search cost collapsed. Expect scrutiny to follow: peer reviewers will ask for the reproduction script, and "trust us, it matched" will stop being an acceptable answer. The uncomfortable corollary, raised by commenters on this result: if only frontier labs can afford exhaustive forensics, the capability that makes standards auditable also widens the gap between what labs can verify and what the public can.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Anthropic — Claude reveals ECDSA curve origins](https://www.anthropic.com/research/ecdsa-curve-origins) | HN Discussion: [675 points, 272 comments](https://news.ycombinator.com/item?id=50122800)*
