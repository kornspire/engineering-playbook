# Project Kickoff Checklist

No billable work begins until every item in this checklist is resolved. Items marked **[BLOCK]** are hard stops — the engagement cannot start without them. Items marked **[RISK]** must be documented if they cannot be resolved before work begins.

The checklist exists because the most expensive problems in software projects are the ones introduced at the start. A scope that was never agreed in writing, environments that were never provisioned, a rollback plan that was never written — these cost ten times more to fix at week eight than at week one.

---

## Scope and contract

- [ ] **[BLOCK]** Statement of work or equivalent document signed by both parties, with an explicit definition of what is in scope and what is not
- [ ] **[BLOCK]** Acceptance criteria defined for the first deliverable — not "working software" but specific, testable conditions
- [ ] Change request process agreed: who can request, who approves, how cost and timeline adjust
- [ ] Out-of-scope items explicitly listed — not just implied by what is in scope
- [ ] Data ownership and IP assignment stated

---

## Environments and access

- [ ] **[BLOCK]** All environments named and their promotion path documented: e.g. `local → staging → production`, not assumed
- [ ] **[BLOCK]** Production access: who has it, under what conditions, and how it is audited
- [ ] Staging environment matches production configuration as closely as practical
- [ ] Access provisioning process documented — no single person holds all credentials
- [ ] Secrets management decided: Key Vault, Secrets Manager, or equivalent. Never plain-text in source control.
- [ ] Repository access granted to Kornspire team; branch protection rules set on main

---

## Data and compliance

- [ ] **[BLOCK]** Data residency requirements confirmed in writing. For Indian clients: whether data must stay within India.
- [ ] **[BLOCK]** Personal data inventory: what PII or sensitive data will the system store, process or transmit?
- [ ] Data classification agreed: what tier is this data, and what does that mean for encryption, retention, and access?
- [ ] DPDPA (India) or other applicable data protection obligations identified
- [ ] Third-party data processors (payment gateways, analytics, email providers) reviewed and approved by client

---

## Architecture

- [ ] Architecture document reviewed with client technical lead before implementation begins
- [ ] **[RISK]** Any technology choice that is hard to reverse documented with the reason and the exit path
- [ ] Scalability assumptions stated: what load is the system designed for, and what would need to change at 10×?
- [ ] Backup and recovery strategy documented before first data is written to production
- [ ] Third-party dependencies evaluated: licence, support status, vendor lock-in risk

---

## CI/CD and deployment

- [ ] **[BLOCK]** Deployment pipeline exists before any code merges to main. We do not merge to main and then figure out deployment.
- [ ] Rollback procedure documented and tested in staging before production go-live
- [ ] Deployment process is automated and repeatable — no manual steps that only one person knows
- [ ] Alerting configured for production errors before launch, not as a post-launch task

---

## Incident and support

- [ ] **[BLOCK]** Named contact on the client side for production incidents, available outside business hours
- [ ] Severity levels defined and response time expectations agreed in writing
- [ ] Escalation path documented: who gets called if the primary contact is unreachable?
- [ ] Support obligations post-launch agreed: what is covered, for how long, at what cost?

---

## Team

- [ ] Engineers assigned to the engagement have read the relevant sections of this playbook
- [ ] Code review process agreed: who reviews, what is the SLA for a first review, who has merge authority?
- [ ] Communication channel agreed and set up: not assumed to be email
- [ ] Status reporting cadence agreed: weekly written update is the default unless the client requests otherwise
