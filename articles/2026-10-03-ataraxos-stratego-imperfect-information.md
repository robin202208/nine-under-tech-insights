# Ataraxos: The AI That Cracked Stratego for a Few Thousand Dollars

> DeepMind failed to beat the world's best Stratego players with 1,024 TPUs. A four-university academic team did it with 16 GPUs — and a belief model that guesses what it cannot see.

## What Happened

One by one, the classic games fell. Deep Blue beat Garry Kasparov at chess in 1997, AlphaGo beat Lee Sedol at Go in 2016, and poker bots have long beaten professionals. But Stratego — a game of hidden armies — held out. Even DeepMind could not build a machine that reliably beat the best human players; its DeepNash (2022) never dominated.

Now a team of researchers from Carnegie Mellon, MIT, NYU, and Stanford has done it. Their AI, Ataraxos, beat Pim Niemeijer — the four-time world champion and long-time world number one — 15 games to one, with four draws. The work was published in Nature in 2026.

Stratego is brutal for machines. Each player controls 40 pieces whose identities are hidden from the opponent: ranks from marshal down to spy, plus bombs and a flag. You win by capturing the opponent's flag. Your opponent knows *where* your pieces are but not *what* they are, and identities are revealed only when two pieces collide. In Texas Hold'em, a machine only has to reason about two hidden cards — 1,326 possible hands. In Stratego, 40 pieces can be arranged in any order: more than a decillion possible setups. Where a chess game lasts about 40 moves, a Stratego game can easily run 2,000. It is also a game of bluffing: move a weak piece like a marshal and your opponent may retreat.

## Why It Matters

Ataraxos learned the same way DeepNash did: by playing against itself, 163 million games in total. Its first innovation was in how aggressively it updated. Hidden information tends to send self-play algorithms around in circles, so the team made big, bold strategy changes early in training and small, careful ones later.

The bigger breakthrough was thinking ahead. Systems like AlphaGo refine a general strategy with a search just before acting, but DeepMind could not make search work in Stratego because the space of possible board states was too large. The Ataraxos team solved this with a second neural network — a belief model — trained to guess the opponent's hidden pieces from how they have been moving. Instead of iterating over every possible arrangement, it samples plausible ones, plays out candidate moves, and picks based on the results.

Its playstyle is uncanny to humans. Its name comes from the ancient Greek word for a state of calm; it does not react impulsively "even in situations where a human would be losing their mind." When its opponent has no reason to suspect a weak spot, Ataraxos leaves that spot alone — almost impossible for a human who knows the secret. "We would watch the bot bluff its way back from a two percent victory probability, very, very casually," said NYU's Eugene Vinitsky.

The cost gap is just as striking. DeepNash trained for two to three months on 1,024 of Google's specialized TPUs — roughly $3 million to $4.5 million at 2025 prices. Ataraxos needed 16 GPUs for a week, plus four GPUs for four days to train its belief model. The team wrote a simulator that runs millions of moves per second on graphics cards, and Ataraxos played about 34 times fewer games than DeepNash while ending up much stronger.

## Impact

Niemeijer played 20 online games over three weeks, earning $100 per win; he managed exactly one, which the researchers attribute to the luck of randomized piece placement. At the 2025 Stratego World Championship, challengers fared worse: Ataraxos won 38 of 40 games. It even shifted the human metagame — players were surprised by how often it tucked its flag into a corner behind just two bombs.

The approach generalizes. The same architecture beat three world champions at Barrage Stratego, mastered the cooperative card game Hanabi, and beat the best bots at the Chinese card game dou dizhu. Board games have fixed rules and clear winners, but the team argues the gap to real-world problems — negotiations, markets, military conflict — is smaller than it looks, since tackling any begins with building a simplified model. Vinitsky points to war gaming: the same techniques could play a scenario forward to see how a strong opponent might respond.

One frontier remains open. Ataraxos cannot yet explain why it makes the moves it makes. "We work on machines that produce strong but also interpretable and explainable strategies," MIT's Gabriele Farina said. "I think we're not quite there yet."

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Ars Technica](https://arstechnica.com/science/2026/10/ai-finally-beat-the-best-stratego-player-in-history-and-did-it-on-a-bu) & [Nature (DOI: 10.1038/s41586-026-11036-y)](https://doi.org/10.1038/s41586-026-11036-y) | HN Discussion: [176 points, 85 comments](https://news.ycombinator.com/item?id=49933740)*
