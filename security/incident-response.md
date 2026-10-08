# Incident Response

An incident is any unplanned event that is affecting or could affect the availability, integrity, or confidentiality of a production system or its data. This includes: service outages, degraded performance, suspected unauthorised access, data corruption, and failed deployments that cannot be immediately rolled back.

The goal of this process is to resolve the incident, limit its impact, and understand what happened — in that order. Post-incident analysis is how we prevent recurrence. It is not how we assign blame.

---

## Severity levels

| Level | Description | Response target |
|---|---|---|
| P1 — Critical | Production system unavailable or data breach suspected | Immediate; escalate within 15 minutes of detection |
| P2 — High | Significant feature unavailable; degraded performance affecting majority of users | Within 1 hour |
| P3 — Medium | Minor feature unavailable; workaround exists; limited user impact | Within 4 business hours |
| P4 — Low | Cosmetic issue, single user affected, or issue detected before user impact | Next business day |

When in doubt, assign the higher severity. It is better to escalate a P3 as a P2 than to underreact to a P1.

---

## The response process

### 1. Detect and acknowledge

Someone identifies an incident — from monitoring, a user report, or direct observation. They:
- Post in the agreed incident channel with: what they observed, when, and the suspected severity
- Claim the incident coordinator role if no one else has

One person coordinates the incident. Multiple people working on the same problem without coordination produces chaos.

### 2. Assess

Before taking any action, understand what is actually happening:

- What is broken? What is still working?
- Who is affected? How many users? Which tenants?
- When did this start? What changed around that time?
- Is this getting worse, stable, or recovering on its own?

Do not skip assessment to start fixing. An incorrect diagnosis produces a fix that does not resolve the issue and may introduce new problems.

### 3. Contain

If the incident involves potential unauthorised access or data exposure, containment is the first priority:

- Revoke the compromised credential, token, or session
- Take the affected system offline if continued operation risks further exposure
- Preserve logs before taking any action that might overwrite them

For availability incidents, contain the blast radius: route traffic away from a failing service, scale down a misbehaving component, enable a maintenance page.

### 4. Communicate

**Internal:** Update the incident channel at regular intervals (every 30 minutes for P1, hourly for P2). Status updates do not need to contain the full picture — "still investigating, initial fix deployed, monitoring" is a useful update.

**Client-facing:** For P1 and P2 incidents, notify the client's named contact as agreed at project kickoff. The notification states: what is affected, what the impact is, what is being done. It does not speculate on cause, assign blame, or promise a resolution time that cannot be met.

**Never speculate publicly** about the cause or scope of a security incident before the investigation is complete.

### 5. Resolve

The incident is resolved when the system is operating within expected parameters and the risk of recurrence from the same immediate cause is addressed. This is not the same as the root cause being fixed — the root cause fix may be a separate piece of work.

Document the resolution in the incident channel: what was done, when the system recovered, and what monitoring confirms the recovery.

### 6. Post-incident review

Every P1 and P2 incident has a post-incident review within five business days. The review is a written document, not a meeting. It covers:

- **Timeline:** When was the incident detected, what actions were taken, when was it resolved?
- **Root cause:** What actually caused the incident? Not the immediate trigger, but the underlying condition that allowed it.
- **Impact:** How many users were affected? For how long? Was any data affected?
- **What went well:** Detection was fast, rollback worked cleanly, communication was clear.
- **What could be improved:** Monitoring gap, unclear escalation path, deployment process that made rollback slow.
- **Action items:** Specific, assigned, time-bounded. "Improve monitoring" is not an action item. "Add an alert for database connection pool exhaustion above 80%, assigned to [name], by [date]" is.

The review is blameless. The question is not "who caused this" but "what in our system allowed this to happen." Systems that punish engineers for incidents produce engineers who hide incidents.

---

## After a security incident

If the incident involved actual or suspected unauthorised access to personal data, the following apply in addition to the standard response:

- Preserve all relevant logs immediately — do not rotate, truncate, or delete
- Engage the client's data protection contact within 24 hours
- Assess whether the incident meets the notification threshold under DPDPA (India) or other applicable law — this is a legal assessment, not an engineering one
- Document everything: what was accessed, when, by whom (if known), and what has been done to contain it
