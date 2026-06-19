# DeepSeek Gets a Pass, 100+ Firms Don't — The Entity List Just Reshaped the AI Supply Chain

> The US Commerce Department held off blacklisting DeepSeek while adding over 100 Chinese firms to the Entity List. The move reveals how AI model development is now entangled with geopolitics — and what it means for the models you depend on.

## What Happened

On June 17, Reuters reported that the US government decided against placing DeepSeek on the Entity List — the Commerce Department's blacklist that prohibits American companies from selling goods and services to listed entities — while simultaneously designating more than 100 other Chinese firms as security risks. The decision reflects the awkward reality that DeepSeek has become deeply embedded in the daily workflows of western developers: it powers coding in VS Code Insiders and Zed Editor, costs pennies compared to Claude, and runs the cheapest API in the market. Blacklisting a tool that American developers spend $2/month on would be politically complicated.

Meanwhile, Z.ai — the maker of GLM-5.2, which recently topped the Artificial Analysis open weights leaderboard — has been on the Entity List since January 2025. The company still released a frontier model that competes with GPT-5.5 on agentic tasks while under sanctions. Being on the list means American firms can't sell to Z.ai, but they can still buy from them and use their open weights models — a loophole that effectively neuters the restriction for software-based AI companies.

## Why It Matters

The Entity List was designed for hardware and dual-use technology. Applying it to AI companies exposes a fundamental category error: you can sanction a chip fab, but you can't sanction an open weights model once it's on HuggingFace.

The practical impact is asymmetric. Chinese AI labs are already cut off from NVIDIA GPUs through separate export controls, so the Entity List adds little additional pain on the hardware front. But for companies like CXMT — China's leading DRAM manufacturer, also on the list — the impact is severe. Memory is a physical product that requires American equipment and IP; you can't download RAM from HuggingFace.

The deeper story is what this says about the weaponization of AI supply chains. The US restricts model access (Claude Fable 5 was pulled from non-US users overnight), restricts hardware (GPU export controls), and now uses the Entity List as a third lever. China's response is to give away frontier models for free — DeepSeek, Qwen, GLM, Kimi, MiniMax — creating a global dependency on Chinese AI infrastructure that runs counter to US policy goals. One HN comment captured the irony: "If Chinese LLMs are successfully making people in the west defend China, then I think we have all the evidence we need to explain why they are giving away their models."

## Developer Impact

For developers building on AI, the Entity List creates concrete uncertainty:

**Model availability is not guaranteed.** If you've standardized on a Chinese open weights model, sanctions could disrupt updates, fine-tunes, and the ecosystem around it. Z.ai proved you can ship frontier models while sanctioned, but the long-term sustainability is untested.

**API access could change overnight.** DeepSeek's API is currently the cheapest path to frontier coding assistance. Political pressure could change that. Having fallback models in your pipeline isn't optional anymore — it's infrastructure hygiene.

**The open weights loophole might close.** Current Entity List rules don't prevent US companies from downloading and using open weights models from sanctioned entities. But this is a policy gap that could be closed, and the precedent of Claude Fable 5's geographic restriction shows that model access can be revoked retroactively.

**Self-hosting becomes a geopolitical hedge.** Alex Ellis's argument for local models as "protection against vendor risk" takes on new meaning when the vendor risk isn't just a company changing its pricing — it's a government changing the rules.

## Our Take

The Entity List was built for a world where technology was embedded in silicon. In a world where frontier AI arrives as a downloadable file, the old frameworks break. Developers should assume that any model — Chinese, American, or otherwise — could become unavailable on short notice. The only durable strategy is to never depend on a single model provider, regardless of how cheap or capable they are today.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights)*
