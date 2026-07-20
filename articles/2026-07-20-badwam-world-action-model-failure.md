# Robots Can Imagine the Right Future and Still Make the Wrong Move

**2026-07-20** | National University of Singapore | Embodied AI · Safety · World Models

---

## What

World-action models (WAMs) are the hot architecture in embodied AI right now. Instead of predicting actions in isolation, they learn joint representations that couple action generation with future world prediction. The reasoning is elegant: a robot's action can — in principle — be checked against its imagined future. If the predicted outcome looks wrong, don't execute. This coupling has been sold as a source of robustness, interpretability, and safety.

A new paper from the National University of Singapore, **BadWAM**, just proved that this coupling is a mirage.

The researchers built a unified evaluation framework that systematically probes world-action models for a specific failure mode: cases where the model's world prediction is *correct* — it accurately imagines what will happen — but the action it produces is *wrong*. This is not a corner case. It's a fundamental architectural flaw. The model dreams right and acts wrong, and the coupling that was supposed to catch errors does nothing to stop it.

The team tested across multiple WAM architectures and embodied benchmarks. The failure pattern is consistent: world prediction accuracy and action quality can and do decouple. A model with 95% world-state prediction accuracy can still produce catastrophic actions 30% of the time. The safety valve that the field had been counting on simply doesn't close.

---

## Why

This matters because the embodied AI field has been building safety architectures on a false premise.

The implicit assumption behind WAMs is that world prediction and action generation share a causal relationship: if you can predict the world, you can navigate it safely. BadWAM shows this is backwards. World prediction is *correlational* — the model learns statistical regularities in visual sequences. Action generation is *causal* — it must intervene in the world. A model can be excellent at the first and incompetent at the second because correlation and causation require fundamentally different representations.

Think of it this way: you can watch thousands of hours of basketball and accurately predict where the ball will go next. That doesn't mean you can dribble past a defender. WAMs are in the same position — world-class spectators, amateur players. The architecture conflates two skills that are not the same skill, and the conflation itself creates the safety illusion.

The paper's second contribution is a taxonomy of how this failure manifests: **persistent errors** (the model consistently chooses the wrong action in the same state), **state-dependent errors** (fine in most states, catastrophic in specific ones), and **prediction-action drift** (world prediction quality and action quality degrade at different rates over time). Each requires different mitigation strategies — and none of them are solved by improving the world model alone.

---

## Impact

BadWAM is not a paper that breaks things. It's a paper that reveals things were already broken and nobody had the instrumentation to see it.

For embodied AI companies shipping robots into physical environments, the implication is stark: your safety architecture may have a silent failure mode. A robot that correctly predicts it will collide with a human but still moves forward is worse than a robot that doesn't predict at all — because the prediction creates false confidence. The paper effectively demands that WAM-based systems add a decoupled action critic that operates independently of the world model, doubling the safety infrastructure.

For the research community, BadWAM is a methodological intervention. It introduces evaluation primitives — targeted adversarial state perturbations, prediction-action consistency metrics — that the field didn't have. The authors are effectively saying: if you're going to claim your model is safer because it predicts the world, you need to prove that the prediction actually constrains the action. Most WAM papers haven't done that. Now they have to.

The deeper lesson is about architectural coupling in AI systems. Every time we design a system where two capabilities are "coupled for safety," we should ask: what happens when the coupling fails silently? BadWAM's answer — for world-action models, at least — is that it fails exactly when it matters most. In states the model has never seen, world prediction degrades gracefully while action quality collapses abruptly. The safety valve opens under pressure — and lets everything through.

---

*Source: [BadWAM: When World-Action Models Dream Right but Act Wrong (arXiv:2607.15207)](https://arxiv.org/abs/2607.15207) — Qi Li, Xingyi Yang, Xinchao Wang, National University of Singapore, July 2026*
