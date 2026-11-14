# Forty-Eight Agents Mapped 1,200 km² of Jungle — and Still Couldn't Find the Paper

> A weekend experiment "rediscovered" a Maya site in 72 hours. The interesting failure was not perception. It was retrieval.

## What Happened

On 18 October, Pietro Miotti — a Google researcher, traveling in Peru, wondering what Hiram Bingham would have done with modern tools — pointed a team of AI agents at public lidar data. Peru was poorly covered; the Maya region is not. His agents rendered roughly 1,200 km² of jungle strips into images and walked through them "like very patient archaeologists": 48 agents looking for pyramids, platforms and plazas while filtering out natural hills, sinkholes and roads. No classifier — just visual inspection against a written guide.

By morning they had flagged dozens of plausible villages. Then Miotti asked the question that turned out to be the whole story: which of these are not already known? An agent reported one hilltop missing from the records — a levelled plaza, an oval pyramid at least 8–12 m tall, a raised courtyard and a flat-bottomed hollow that might have been a rainwater reservoir. The agents checked the raw point cloud to prove the shapes were ground returns, not canopy artifacts: brown ground returns tracing the plaza beneath 10–17 m of forest. They drafted a report, translated it into Spanish, and emailed it to Mexico's National Institute of Anthropology and History (INAH) with coordinates in a confidential annex. On 21 October, 72 hours after the idea, INAH replied that no registered archaeological site existed at that location.

On 11 November Miotti added a correction. The site had already been mapped in a peer-reviewed paper published more than a year earlier (Estrada-Belli et al., *Journal of Archaeological Science: Reports*, 2025). His agents had missed it during their literature pass; INAH's register simply did not contain it. Sarah Parcak of UAB noted that "this is why we have peer review," and Ivan Šprajc, a coauthor of that paper, told *The Verge* that "just spotting something does not add much to our knowledge."

## Why It Matters

The failure was mechanical, and Miotti diagnosed it himself: archaeologists do not publish coordinates, precisely to deter looting. So "is this already known?" cannot be a database lookup. It requires comparing the candidate's geometry against figures in published PDFs — rasterize the figure, recover its scale, match a plaza-plus-pyramid layout across thousands of papers. That is a multimodal retrieval problem, and the agents had no stage for it. Their pipeline was: detect, then query a registry. Nothing in between.

That gap generalizes far beyond archaeology, because the authority usually sits in the missing stage. Registries lag the literature by years and record paperwork, not knowledge; unpublished surveys stay invisible; and the decisive evidence is often a figure, not a string. An agent that can read a lidar raster but not a journal figure has a verification blind spot exactly where novelty is decided.

Note what did not fail. Perception was good: the point-cloud slices really did trace built structures under canopy, and Šprajc — who mapped the site himself — conceded the lidar interpretation was decent. The technique was old news as well; archaeologists have reused environmental lidar this way for years, including a 2024 study that revealed a whole Maya city. Every local step was competent. The single false claim came from validating a true observation against the wrong oracle: a national register instead of the literature.

## Impact

First, novelty checks must become a first-class tool, not a line in a prompt. A discovery-type pipeline should have a mandatory prior-art stage that returns candidate matches with citations and figures, and should refuse to say "new" without one. That is an engineering problem — figure extraction, geo-referencing, layout similarity — and it is missing from most agent stacks, which happily substitute one web search for a literature review.

Second, a registry is not ground truth, and neither is a search index. When an agent's "is this known?" step ends at a single API, it confuses the absence of a record with the absence of knowledge. A null result should come back unresolved, with an explicit confidence, not as confirmation.

Third, disclosure has costs. Šprajc says he already receives a steady stream of AI "discoverer" emails, and warns that publicizing locations can attract looters. Report privately to institutions first, publish after.

The optimistic reading survives, though. An outsider with no domain training came within one literature search of the state of the art in 72 hours on a personal budget, and archaeologists have since asked him for the workflow. The next version of this pipeline should be judged less on what it can find than on how quickly it can prove that someone else found it first.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Sources: [Pietro Miotti](https://pietromiotti.com/latest/maya-site) and [The Verge](https://www.theverge.com/ai-artificial-intelligence/1032125/ai-agents-maya-site-archeology-discovery) | HN Discussion: [1,035 points, 392 comments](https://news.ycombinator.com/item?id=50452455)*
