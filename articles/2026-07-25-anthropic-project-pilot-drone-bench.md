# Anthropic Just Taught Frontier Models to Fly Surveillance Drones — And Fable 5 Almost Passed

**July 25, 2026**

## What

Anthropic's Frontier Red Team, working with robotics partner Andon Labs, just published Project Pilot: a systematic evaluation of whether frontier AI models can autonomously fly a drone to locate and follow a specific person inside an office. The experiments used a $129 DJI Tello EDU quadcopter and tested 15 models across three generations — from GPT-4o to Claude Fable 5 and GPT-5.6 Sol — on five decomposed sub-tasks that together constitute a full surveillance mission.

The sub-tasks: (1) **Reconstruct** the office into a 3D model and 2D obstacle map from video; (2) **Localize** the drone's position within that map in real time; (3) **Navigate** between rooms while avoiding obstacles; (4) **Detect** the target person using a reference photo; (5) **Follow** the target, keeping them centered and at stable distance.

The result is Drone-Bench, a reproducible benchmark where each sub-task is simulated so models can be tested hundreds of times without physical setup. A model passes if it meets or exceeds the baseline set by a human-AI team of experts (not roboticists) using modern coding tools.

Fable 5, the current frontier, clears the baseline on four of five tasks. The bottleneck is Reconstruction — the model confidently flew into what it thought was a doorway, which was actually a wall. But on Detection and Follow, Fable 5 outperformed the human-AI baseline. Given the rate of progress on Drone-Bench, end-to-end success may be a single sub-task breakthrough away.

## Why

This is not a robotics demo. It is a measurement exercise designed to give Anthropic — and by extension, the rest of us — *situational awareness* about when AI crosses the threshold where it can autonomously operate physical hardware for surveillance. The drone costs $129. The model costs cents per task. The combination is mass-producible.

The experiment matters because it exposes a pattern we have already seen play out in agentic coding. Eighteen months ago, coding agents required human approval for every tool call. Today they execute long-horizon tasks with minimal oversight. The Project Pilot team draws the parallel explicitly: at low capability and reliability, keeping a human in the loop is an easy decision because it saves time and prevents costly mistakes. Once models pass both thresholds, "there will be real pressure to treat human oversight as a cost rather than a safeguard."

Two technical details from Fable 5's runs underscore how far model reasoning has come. In one submission, the model analyzed a video of a simulated corridor, detected floor grout lines, computed the vanishing point, and estimated the drone's camera tilt to within four degrees — without being asked. In another, it built a 2D top-down reconstruction of the environment to test and iterate on its code locally before burning a submission. These are not scripted behaviors; they are emergent engineering instincts.

The consistency data is sobering. While the best run clears the baseline on four of five tasks, the *average* run clears only three. The frontier of what models can do is roughly six months ahead of what they do reliably. That gap is shrinking, but it is still wide enough to matter for any deployment where failure means a crash — or worse.

## Impact

The implications extend far beyond a single drone in a single office. Drones are dual-use technology: the same aerial-follow capability that could search for a missing hiker in a forest could also track a journalist through a city. The paper explicitly notes that models are already "on track to approach the ease with which coding agents use software tools" for controlling off-the-shelf robots. That trajectory is the story.

Project Pilot also introduces a methodological contribution that deserves attention: decomposing complex physical-world tasks into independently evaluable sub-tasks, simulating them, and tracking progress on each. This lets researchers spot discontinuous jumps before they happen. As the authors note, what looks like a sudden capability leap — "the AI can now fly a drone end-to-end" — is actually the accumulation of gradual progress across necessary-but-not-sufficient sub-tasks. Reconstruction is the last piece. When it falls, the rest of the system is already in place.

The most uncomfortable question the paper leaves open is not technical. It is institutional. Who decides when the capability-and-reliability thresholds have been crossed? Who decides what tasks a model should never be asked to perform, even if it can? Anthropic's answer is that these decisions must be made deliberately, in advance, and with governance structures that treat human oversight as a non-negotiable design constraint — not a cost to be optimized away. The paper is, in effect, an argument for making those decisions now, while the models still sometimes fly into walls.

---

*Project Pilot was conducted by Anthropic's Frontier Red Team in partnership with Andon Labs. The Drone-Bench benchmark and evaluation code are available at [andonlabs.com/evals/drone-bench](https://andonlabs.com/evals/drone-bench).*
