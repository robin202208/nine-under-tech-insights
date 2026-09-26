# Google's First Orbital TPU Ships Next Week — the Hard Part Is Getting the Heat Out

> Project Suncatcher's prototype is not a data center. It is a vibration, radiation and thermal experiment that decides whether orbital AI compute is a real engineering path.

## What Happened

On 24 September, Google published a Project Suncatcher fact page confirming that its first prototype satellite will fly "next week" with Planet Labs on SpaceX's Transporter-18 rideshare. The payload is not a server rack but an instrumented test article carrying Google TPUs, built to answer three questions no ground test can.

The first is mechanical. A ride to low Earth orbit lasts about ten minutes and subjects the craft to sustained loads up to 10 g, while individual components like the TPU chips can see 50–100 g. Google shook the satellite on all three axes to reproduce launch frequencies; the hardware "held up to the force."

The second is radiation. Outside the atmosphere, solar events and cosmic rays flip bits. Google ran TPUs under AI workloads in a proton beam at UC Davis's Crocker Nuclear Laboratory to watch how errors propagated. Initial results, echoed in the company's November 2025 paper (arXiv 2511.19468): Trillium-generation TPUs survive a total ionizing dose equal to a five-year mission without permanent failure, and their bit-flip behaviour is characterized.

The third is heat. In a vacuum, heat leaves a satellite only through radiators. Google tested heat pipes and radiators in a thermal-vacuum chamber; the prototype now flies that hardware.

Everything else is still design. The whitepaper outlines an 81-satellite cluster roughly one kilometre in radius, linked by free-space optics that Google compares to hitting a coin-sized target from miles away while both points move. A two-satellite laser demonstration is planned for 2027.

## Why It Matters

The reason to put AI in orbit is power. In a dawn–dusk sun-synchronous orbit a satellite sits in near-continuous sunlight, and Google claims a panel there can generate up to eight times more energy annually than an equivalent panel on Earth. That qualifier matters: the eight-times figure describes one orbital band, not low Earth orbit in general.

The harder constraint sits at the other end of the chain. Radiative cooling obeys Stefan–Boltzmann: a panel at 300 K with emissivity 0.9 rejects roughly 413 W/m² per face — about 827 W/m² when both faces see deep space — and real panels surrender part of that to sunlight and Earth infrared. On the input side, 1,361 W/m² of sunlight at 22% cell efficiency yields about 300 W/m². So every megawatt of compute needs on the order of 3,340 m² of array and 1,210 m² of radiator that never faces the Sun. Both terms scale as area, area is mass, and mass is launch cost.

That is why the geometry turns absurd at gigawatt scale. A recent paper by Geoffrey W. Marcy (arXiv 2603.28829) calculates that 5 GW in sun-synchronous orbit requires arrays around 4 × 4 km. Google's answer, laid out in the whitepaper, is architectural: instead of one monolithic structure assembled in space, fly many small satellites in tight formation — an approach it argues scales into the terawatt capacity available in that band.

The externalities are measurable. Marcy notes a 4 × 4 km array would span about 0.4 degrees of sky — comparable to the Moon — and shine at magnitude −5 to −7, a hundred times brighter than the brightest stars, for ninety minutes after sunset and before sunrise. Dozens of them would read as a north–south chain of industrial objects crossing the sky.

## Impact

None of this is settled by this launch. The prototype returns telemetry on shock, dose and thermal behaviour — it is not a training run, a capacity milestone, or evidence that orbital compute is economical. The number that decides the last question sits elsewhere: Google's learning-curve analysis projects launch to LEO reaching roughly $200/kg by the mid-2030s, versus a current market price well above $1,000/kg. If that curve does not materialize, Suncatcher remains a paper architecture with an expensive proof point.

For infrastructure planners, the takeaway is a set of hard constraints. Commercial accelerator silicon is only viable inside the magnetosphere, so orbital compute stays a low-Earth-orbit technology rather than a general escape from terrestrial siting. Heat rejection, not solar collection, is the area-limiting term. And nothing in orbit is repairable, which turns every component failure into disposal cost.

Watch the 2027 laser-link demonstration, not this launch. Close-proximity formation flight at kilometre scale, with optical links holding alignment while both spacecraft move, is the capability the architecture actually depends on, and the piece Google has not yet flown.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Sources: [Google Research — Project Suncatcher](https://blog.google/innovation-and-ai/models-and-research/google-research/google-project-suncatcher-facts/), [arXiv 2511.19468](https://arxiv.org/abs/2511.19468), [arXiv 2603.28829](https://arxiv.org/abs/2603.28829) | HN Discussion: [225 points, 512 comments](https://news.ycombinator.com/item?id=49830606)*
