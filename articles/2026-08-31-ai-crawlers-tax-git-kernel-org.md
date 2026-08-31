# The AI Crawler Tax: 98% of git.kernel.org Traffic Isn't Human

> AI training crawlers are consuming roughly 20% of Linux's infrastructure capacity — by scraping commit pages in the dumbest way possible, through your TV.

## What Happened

Konstantin Ryabitsev, the maintainer of git.kernel.org, published hard numbers on what AI crawlers have done to one of the most important pieces of open-source infrastructure on earth. The headline: legitimate requests are only about **2% of daily traffic**. Everything else — roughly 6 million requests per day — is scrapers trying to harvest Linux's commit history for training data.

Why is git.kernel.org so attractive to model builders? Because Linux development is guaranteed LLM-free. Training a model on content produced by other models gives it "the equivalent of a digital prion disease," so a source that is verifiably pre-AI — like 1.48 million kernel commits across 922 forks — is worth its weight in gold.

The staggering part is *how* the crawlers do it. The kernel team makes everything clonable: a single `git clone` gives you the entire history in one efficient transfer. Instead, crawlers render **every commit as HTML, one page at a time**, and parse it. With cgit's URL space, a single fork of linux.git can generate over a billion valid URLs — and the scrapers are hitting them across 922 duplicate forks. That is several billion URLs fetching 922 copies of the same 1.48 million commits.

The economics are brutal: across 5 geo-distributed nodes with 90 cores, **14–16 cores are permanently occupied rendering commits for scrapers** — about 20% of total capacity, spiky when "swarms" descend. Defenses have escalated into an arms race: user-agent bans → IP bans → ASN bans → Anubis proof-of-work challenges (computing a sha256 with N leading zeroes). Difficulty 4 worked for months, then difficulty 5 worked for a few more, and now **33% of bots solve the PoW and get through**. The crawlers now arrive from millions of residential and mobile IPs — proxy SDK monetization, where your smart TV quietly resells your bandwidth.

## Why It Matters

This is a textbook tragedy of the commons playing out in real time. The kernel team deliberately made its data maximally available — "we may not be around forever, so clone the repos." That generosity has been weaponized by an industry that externalizes its training-data costs onto volunteers and non-profit infrastructure.

Two deeper lessons. First, the crawlers are not just greedy, they are *wastefully* greedy: they chose HTML scraping over git clone, likely because raw git objects are harder to filter and the HTML pages are trivially parseable — burning ~1,000x more server resources than necessary to get identical data. When the marginal cost of computation is someone else's, there is zero incentive to be efficient. Second, proof-of-work is not a durable defense. Anubis worked precisely until the scraped data became valuable enough to justify burning real compute — and then it didn't. Any defense that can be priced gets outbid.

## Impact

For the open-source ecosystem, the consequences are already visible: kernel.org is **turning off features and gating expensive actions** to shrink the crawlable URL space. Anonymous access to one of computing's most important public archives will get worse — more hoops, less functionality. For developers, expect more friction when using public git infrastructure, and expect other high-value open archives (mailing lists, bug trackers, docs) to follow the same playbook.

For the AI industry, this is a leading indicator of a structural cost that keeps growing: clean training data is finite, and the race to secure it is degrading the very infrastructure that produced it. Someone will eventually pay — either in closed archives, in scraping defense costs, or in the "digital prion disease" of models trained on model output. The kernel team's data remains free to anyone who asks. You just may have to jump through more hoops to get it.

---

*Published by [九地之下 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Creepy crawlies — Konstantin Ryabitsev (people.kernel.org)](https://people.kernel.org/monsieuricon/creepy-crawlies) | HN Discussion: [910 points, 420 comments](https://news.ycombinator.com/item?id=49491791)*
