# 50,000 Sites, One $200 VM — Shopify Quick and the AI-Era Internal Platform

**Date:** 2026-06-14
**Category:** Infrastructure / Developer Tools
**Source:** Shopify Engineering Blog

---

## What Happened

Shopify open-sourced the architecture behind Quick, its internal hosting platform that runs 50,000+ employee-built sites on a single VM costing $200/month. Over half of Shopify's workforce has created at least one Quick site, and the platform has spawned an emergent internal ecosystem of shared libraries, multiplayer games, dashboards, and prodding tools—all secured behind Identity-Aware Proxy with zero-config deployment.

The architecture is aggressively simple. Every site is a folder in a Google Cloud Storage bucket. gcsfuse mounts that bucket as a local filesystem. NGINX sits in front with a wildcard config that routes `mysite.quick.shopify.io` to the corresponding folder. Google IAP wraps the whole thing so every request arrives pre-authenticated. Deployment is a thin wrapper around `gcloud rsync`—drop a folder of HTML and assets, get back a URL.

What makes Quick more than static hosting is a single backend server exposing six zero-config APIs: a realtime database (Firebase-inspired, with subscriptions), file uploads, AI (LLM and image generation via internal proxy, no API keys needed), BigQuery data warehouse access, WebSockets for multiplayer, and identity (name, title, team, Slack handle from IAP headers). All six are client-side callable with zero authentication boilerplate because the entire platform is internal-only.

The timing was accidental but decisive. Quick launched in July 2025. By December 2025—when AI code generation reached the point where anyone could produce a working HTML site from a prompt—adoption exploded. The growth curve went vertical. People who'd never written HTML were suddenly shipping sites because AI could generate the code and Quick gave them somewhere to put it.

---

## Why It Matters

Quick didn't lower the cost of hosting—$200/month is rounding error for any company. It lowered the cost of sharing. Before Quick, building something at Shopify was easy; getting it in front of anyone else meant navigating deploy pipelines, provisioning infrastructure, and figuring out auth. The gap between "I built a thing" and "you can see the thing" was the real bottleneck. Quick collapsed it to seconds.

The constraints are the design philosophy. No permissions model—every site is visible to every employee. No concept of site ownership—anyone can overwrite any subdomain. No custom backends, no cron jobs, no feature requests accepted. Each "no" reinforces the platform's core proposition: maximum creative speed through minimum surface area. The team has gotten good at saying no to feature requests and instead showing users how the existing six APIs already solve their problem differently.

The AI amplification effect is underappreciated. Quick wasn't built for AI—it was built before AI code generation became ubiquitous. But the combination created a force multiplier: AI lowers the barrier to producing HTML, Quick lowers the barrier to publishing it. Together they turned every Shopify employee into a potential tool builder. A Google Meet outage spawned a WebRTC replacement in 15 minutes. A game jam drew 140+ multiplayer entries. Designers build custom team tools without involving engineering.

---

## The Architecture Decision That Changed Everything

The team initially considered giving each Quick site its own SQLite database, a natural choice for per-site isolation. gcsfuse killed that idea—SQLite doesn't play well with FUSE-mounted filesystems. They pivoted to a single CloudSQL database behind a Node.js server (later migrated to Go for memory management and parallelism), which turned out to be the better architecture anyway. One server, one database, every site sharing the same backend. The simplicity meant one team could maintain it all.

The emergent ecosystem was unplanned. Because Quick sites are just web pages on the same domain, one site can import JavaScript from another. People started publishing shared libraries—Figma-style comments, voice integration, analytics, achievement systems—and building landing pages to document them. Quick became its own internal npm, with discovery happening through the same URLs that serve the sites. No package registry, no versioning, just URLs you can import. It works because every consumer is also a potential contributor, and every site's source is visible to everyone.

---

## The Bigger Picture

Quick forces a re-examination of what internal platforms should look like in the AI era. The dominant enterprise pattern for a decade has been: provision infrastructure, configure CI/CD, manage permissions, audit access. Quick inverts all of it. The assumption is that internal trust eliminates entire categories of complexity. No auth UI because IAP handles it. No access controls because every employee can see everything. No deploy pipelines because rsync is enough.

The $200/month number is the killer detail. It means the marginal cost of one more internal site is effectively zero. Every enterprise has dormant ideas—dashboards nobody prioritized, tools teams asked for but never got, prototypes that died on localhost. Quick demonstrates that the infrastructure barrier to shipping those ideas can be eliminated, and that when you eliminate it, the ideas ship themselves.

The open question is whether this pattern generalizes beyond Shopify's culture. Tobi Lütke calls the environment Lehrwerkstatt—a learning workshop where knowledge spreads through proximity. Quick amplified that culture; it didn't create it. Companies with stronger silos and stricter access controls may find that technical simplicity collides with organizational complexity. But for engineering organizations where "build and share" is the default mode, Quick is a template worth copying.

---

*Full report: [Shopify Engineering — Quick](https://shopify.engineering/quick)*
