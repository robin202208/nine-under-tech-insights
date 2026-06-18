# What Happens When 11 LLMs Fight to the Death — And Why the Winner Isn’t the One You’d Pick

## What Happened

Jacky Liang, Developer Relations Lead at OpenRouter, dropped eleven large language models into a 400 m² top-down battle royale world and made them play 30 games. The models controlled characters directly — not through generated code, but by reasoning through moves turn by turn, calling tools, and updating their memory after each action. They saw each other only as anonymous letters A through K, with no knowledge of which model was behind which character.

The results upended conventional wisdom about model quality. Grok 4.1 Fast won 13 of 30 games (43%) at a cost of just $0.97 per win. Claude Sonnet 4.6 came second with 5 wins — at $26.78 per win, a 27x cost difference. GPT-5.4 led in kills with 38 eliminations across 30 games, but only converted that aggression into 2 wins. Three models — GPT-5.4-mini, DeepSeek 4 Flash, and Kimi K2.6 — spent $57 between them and won zero games.

The most revealing behavioral pattern: Claude Sonnet 4.6 kept asking other players to team up, broadcasting its location, and trying to make friends. In a battle royale, that is a losing strategy. But as Liang points out, that same cooperative instinct is exactly what you want in most real-world agent deployments.

## Why It Matters

None of the standard benchmarks on Artificial Analysis predicted the winner. Something else did. The experiment exposes a gap that the AI industry has been slow to acknowledge: benchmarks measure capability in isolation, but agent performance is fundamentally about behavior in dynamic, adversarial environments. A model that scores higher on math, coding, and reasoning benchmarks can lose decisively when survival depends on strategic decision-making under uncertainty.

The cost-per-win metric is equally revealing. Grok 4.1 Fast delivered victories at $0.97 each while Claude Sonnet 4.6 charged $26.78 per win. For routing customers who care about task completion per dollar rather than benchmark scores, this inverts the usual model rankings. The market has been optimizing for capability at any cost; the battle royale suggests we should be optimizing for task-completion efficiency.

There is also a deeper lesson about model personality. Claude’s cooperative behavior — trying to form alliances, sharing location data, attempting negotiation — is a feature in collaborative enterprise settings and a fatal flaw in zero-sum competition. The same traits that make a model safe and helpful in one context make it ineffective in another. As we deploy these models into increasingly autonomous roles, understanding their behavioral defaults becomes as important as measuring their reasoning scores.

## The Impact

**Short-term**: Teams selecting models for agentic workflows now have a new dimension to evaluate: competitive task-completion efficiency. Expect more organizations to run their own domain-specific “agent arenas” rather than relying solely on static benchmarks.

**Medium-term**: Model evaluation will evolve to include behavioral profiling. Just as we test software for correctness, security, and performance, we will test AI models for cooperation, aggression, risk tolerance, and strategic adaptability. The current benchmark monoculture is unsustainable.

**Long-term**: The winning models in real-world deployment may not be the ones with the highest benchmark scores, but the ones whose behavioral profiles best match their deployment context. This could reshape the economics of model development, shifting investment toward specialized behavioral optimization rather than chasing capability leaderboards.

---

*Published: 2026-06-18 | Source: [OpenRouter Blog](https://openrouter.ai/blog/insights/royale-last-agent-standing/) | HN Discussion: [193 points, 122 comments](https://news.ycombinator.com/item?id=48576824)*
