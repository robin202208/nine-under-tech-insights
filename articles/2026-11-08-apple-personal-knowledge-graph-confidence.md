# 181 Classes, 278 Predicates: The Family Tree Your Mac Builds Without Asking

> Two daemons have been writing a relational database about who you know since macOS 13. It scores your mother at 0.96, and it keeps a table for the answers it later decides were wrong.

## What Happened

On October 28, Dario Pasquini and Michał Bazyli published an anatomy of Apple's Personal Knowledge Graph, reconstructed on macOS 26.6.2 (build 25G83). Apple's only public description of the subsystem is a two-sentence man page for `knowledgeconstructiond`, a daemon that "analyzes content on the device to build a general purpose knowledge graph" alongside `intelligenceplatformd`. Every Mac since macOS 13 ships both.

The store is fifty-one SQLite databases, in plaintext, under `~/Library/IntelligencePlatform`, inside a TCC Full Disk Access zone. The interesting one is `graph.db`, a triple store written in a vocabulary of 181 entity classes and 278 predicates. Each row carries a confidence between 0 and 1, its sources, and a timestamp. Relationships are reified — an edge is two rows sharing a `relationshipId`, one naming the relationship type, one pointing at the other entity — precisely so that the edge itself can hold a confidence and later be retracted.

Inputs include Contacts, Photos faces, Calendar attendees, Family Sharing ages, the Medical ID emergency contact, Siri Suggestions mined from Mail and Messages, App Intents donations and installed apps, plus separate tables for photo co-appearance, a communication log (who, when, which app, direction — no content), location, and device signals.

Inference mixes copying, rules and models. Two CoreML XGBoost classifiers take 144 mostly behavioral features — shared private email domains, age-difference category, iCloud family membership, contact frequency by weekday — and write a class and probability into a table of seventeen relationship columns recomputed daily. Twelve analyzers in Photos score partner, parent, child, coworker and friend from co-appearance on trips and holidays. With Apple Intelligence on, the on-device LLM reads messages to attach a relationship to a face. The authors also shipped `iKnow`, a read-only dashboard built on the Python standard library that binds `127.0.0.1` and renders your graph without anything leaving the Mac.

## Why It Matters

The headline finding is not a model. It is an ontology. A hundred and eighty-one classes and 278 predicates is a product decision frozen into SQLite: someone enumerated every kind of person, place, activity and relationship the system may believe in, then added 46 relationship types so beliefs about beliefs could carry their own weight. Fifty-seven classes describe an activity — sleeping, shopping, gaming, waking up, going to bed.

Confidence is what makes this more than a cache. Once an edge has a probability, it has a lifecycle: superseded rows move to `expired_stable_graph`, which keeps the confidence and the sources. In the authors' testing, old records stayed readable past the store's roughly 48-hour prune watermark, and the scorecard still ranked two people the graph had dropped the day before, both known only from a calendar invitation. Conclusions outlive their evidence. The join is what makes that dangerous: a daycare name plus both phone numbers, a trip's dates and time windows, who will be there, and the machine's installed apps down to the password manager.

The other uncomfortable property is scope. This is not a profile of the user; it is a profile of everyone the user has corresponded with, linked to each other through shared events, and none of them were asked. On the scorecard, mother, father and daughter sit at 0.960 — the ceiling — because nine independent checks agree, eight of nine on the mother. Brother, son and sister sit at 0.500. What is missing is any way to look or argue: Apple's researchers published the technique in 2023, but there is no documentation of the integration, no inspector, and no supported way to correct an inference.

## Impact

For anyone building on-device personalization, this is the reference architecture worth copying and the trap worth avoiding. Copy the schema discipline — explicit classes, reified edges, per-edge confidence, provenance, retraction. Avoid the part where the store is undocumented, unauditable and uncorrectable by the person it describes. Ship the read and correct path before the inference path, because a wrong 0.96 is worse than no edge.

For the platform argument, "local equals private" now depends on what else can reach that TCC zone. The authors' follow-up is a thought experiment on weaponizing the graph: extortion built on joins nobody chose to make, reasoned about by an abliterated model that will not refuse. The threat model is not a model stealing your data — it is the graph having already assembled the argument.

For developers, there is a practical step: run `iKnow` on your own Mac and read what has been inferred about your family.

---

*Published by [9D量子 Tech Insights](https://github.com/robin202208/nine-under-tech-insights) | Source: [Anatomy of Apple's Personal Knowledge Graph](https://8rank.ai/game/apple-intelligence-personal-knowledge-graph) | HN Discussion: [598 points, 346 comments](https://news.ycombinator.com/item?id=50403097)*
