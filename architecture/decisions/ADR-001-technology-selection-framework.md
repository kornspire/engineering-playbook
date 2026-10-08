# ADR-001: Technology Selection Framework

**Status:** Accepted  
**Date:** 2026-09-23  
**Applies to:** All greenfield engagements where Kornspire has genuine technology choice

---

## Context

Kornspire works across client environments and does not impose a fixed technology stack. However, when a client does not have existing technology constraints — a greenfield project, a rewrite, or a new product line — we are asked to recommend a stack. Without a principled framework, those recommendations reduce to personal preference or familiarity, which is not in the client's interest.

This ADR documents the evaluation framework we apply when selecting or recommending technology for a new engagement. It does not dictate outcomes; it disciplines the process.

---

## Decision

We evaluate technology choices against four criteria, in this order:

### 1. Operational fit

The most important question is not "what is the best technology for this problem?" but "who will operate and maintain this after Kornspire leaves?"

If the client has an in-house team with strong Python expertise, choosing Go because it is technically better-suited for the use case creates a support problem that will outlast the project. If the client intends to hand the system to a third party, the technology must be mainstream enough that engineers are available to hire.

We make the operational assumptions explicit at kickoff. We do not assume the client will always have Kornspire to maintain what we build.

### 2. Reversibility

Technology choices vary in how easy they are to undo. A choice of ORM or testing framework is easy to reverse. A choice of database engine, cloud platform, or messaging architecture is expensive to reverse. A choice of a platform whose vendor controls migration paths (certain managed databases, proprietary workflow engines) may be nearly irreversible.

We do not avoid lock-in categorically — a managed service that solves a real problem is often worth the dependency. We make the lock-in explicit, document the exit path, and ensure the client understands the cost of leaving.

### 3. Boredom over novelty

For production systems that must be stable for years, established technology with a large support surface beats novel technology with a marginal advantage. Boring technology is well-documented, has solved edge cases, and has available engineers.

Novel technology earns consideration when it solves a problem that established technology handles poorly, not when it is new and interesting. The question is: "What real problem does this solve that the boring option does not?"

### 4. Data layer independence

Wherever practical, application code should be independent of the specific database or storage implementation. This is a specific case of reversibility, called out separately because the data layer is the hardest component to migrate and the one most subject to vendor pressure.

We prefer query builders over ORMs that generate opaque SQL, portable schema migration tools over cloud-specific ones, and documented data formats over proprietary serialisation.

---

## What this framework does not cover

This framework applies to technology selection for the system being built. It does not apply to:

- Development tooling (IDEs, local environment setup) — engineer preference
- Internal tooling that is not deployed to production — pragmatism rules
- Client-mandated technology — the client's system, the client's choice, and our job is to work well within it

---

## Consequences

Applying this framework adds a few hours of upfront work to a kickoff: documenting operational assumptions, making lock-in explicit, and writing down why each major choice was made. It prevents a larger category of problems: technology choices that seemed reasonable at selection but created operational or maintenance costs the client was not prepared for.

The ADRs that follow this one document specific decisions made under this framework.
