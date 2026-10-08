# Kornspire Engineering Playbook

This repository documents how Kornspire engineers software. It covers the decisions we make, the standards we hold ourselves to, the practices we apply on every engagement, and the patterns we reach for when a problem calls for them.

It exists for two reasons.

**For clients and partners:** The most honest thing a consultancy can do is show its working. If you are evaluating whether to trust Kornspire with a significant project, reading how we think about architecture decisions, code review and incident response will tell you more than a capabilities slide. You can hold us to what is written here.

**For our own team:** Documented practices are reproducible practices. A new engineer should be able to read this and understand not just what we do, but why — and push back on us if the reasoning no longer holds.

---

## Who we are

[Kornspire Private Limited](https://kornspire.com) is a technology company incorporated in India (CIN: U62091PN2026PTC260635). We design, build and maintain software, web and mobile platforms, SaaS and PaaS products, and provide IT consulting, cloud, data, cybersecurity and emerging-technology engineering for organisations in India and abroad.

We also build and operate [Upkilo](https://upkilo.com) — our own multi-tenant SaaS platform for appointment-based businesses. It is in production with paying customers, and we carry the infrastructure bill, the support load and the 2am incidents. We mention it because a firm that operates its own production system at scale has made different decisions — and paid different prices — than one that only advises.

---

## What is in this playbook

| Section | What it covers |
|---|---|
| [How we work](how-we-work/) | Project kickoff checklist, code review standards, testing philosophy |
| [Architecture decisions](architecture/decisions/) | ADRs — significant choices we have made, the options we considered, and why we chose what we chose |
| [Architecture patterns](architecture/patterns/) | Reusable patterns for recurring problems |
| [Security](security/) | Baseline practices and incident response |
| [Technology radar](technology-radar/) | How we evaluate technology, and our current recommendations for greenfield projects |

---

## How to read the ADRs

An Architecture Decision Record (ADR) documents a significant technical decision: the context that forced the decision, the options that were on the table, and the reasoning behind the choice made. It also records when the decision was made, so future readers know how old the reasoning is.

ADRs are not edicts. They are the best available reasoning at the time they were written. When a decision turns out to be wrong, or when the context changes enough that the reasoning no longer applies, the ADR is superseded — not quietly deleted.

---

## A note on technology choices

Kornspire adapts to the technology environment of each client engagement. We do not insist on a preferred stack when a client has an existing codebase, an in-house team with different skills, or contractual reasons to stay on a platform. The technology radar and the ADRs in this playbook reflect our recommendations for greenfield work where we have genuine choice. They are starting points for a conversation, not requirements.

---

## Licence

The documentation in this repository is published under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). You may use, adapt and redistribute it with attribution.
