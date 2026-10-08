# Security Practices

These are the security baseline requirements for all systems Kornspire builds or maintains. They are not a comprehensive threat model for any specific system — that is produced per engagement — but they are the floor below which we will not operate.

Items prefixed **[MUST]** are non-negotiable and will be raised as findings in any code review or architecture review. Items prefixed **[SHOULD]** are strong recommendations with documented exceptions allowed.

---

## Secrets and credentials

**[MUST]** No secrets, API keys, tokens, passwords, or connection strings in source code. This includes strings that are "only for development" — development credentials rotate too slowly and code repositories are not temporary.

**[MUST]** All secrets stored in a managed secrets service: Azure Key Vault, AWS Secrets Manager, HashiCorp Vault, or equivalent. Environment variables in CI/CD are acceptable for non-production secrets when a managed service is not available, with the understanding that they are in platform logs and must be treated accordingly.

**[MUST]** Secrets rotated on any personnel change. A departing engineer's access does not expire when their employment does — it expires when the secrets they had access to are rotated.

**[SHOULD]** Secrets rotated on a schedule regardless of personnel changes. 90 days for long-lived API keys.

**[MUST]** Different secrets for each environment. The production database password is not the same as the staging database password.

---

## Authentication

**[MUST]** Use an established identity provider or authentication library, not a hand-rolled implementation. The list of ways to implement authentication incorrectly is longer than the list of ways to do it correctly. Managed options: Azure Entra ID, AWS Cognito, Auth0, Supabase Auth.

**[MUST]** Passwords are never stored. A hash (bcrypt, Argon2id, scrypt) is stored. If the database of hashes is exfiltrated, the original passwords must not be recoverable in reasonable time.

**[MUST]** Password reset flows invalidate the token after use and after a time limit. A reset token valid for 72 hours that can be used multiple times is not a reset token.

**[MUST]** Multi-factor authentication available and encouraged for administrative and high-privilege accounts. Required for accounts with production database access.

---

## Authorisation

**[MUST]** Default deny. Every resource is inaccessible until access is explicitly granted, not the reverse.

**[MUST]** Authorisation checks at the API/service layer, not only in the UI. The UI communicating "you don't have access" does not prevent a direct API call.

**[MUST]** Never trust the client to report its own privilege level. The server determines what a user may do from the authenticated identity, not from a field in the request.

---

## Input validation and injection

**[MUST]** All external input validated at the system boundary — request parameters, headers, file uploads, webhook payloads. Validation includes type, range, and format checks, not just presence.

**[MUST]** Parameterised queries for all database operations. String concatenation to build SQL is not acceptable regardless of the source of the input.

**[MUST]** HTML output escaped at the template or component layer. Framework-level escaping (React's default, Django's template engine) is acceptable; bypassing it (dangerouslySetInnerHTML, `mark_safe`) requires a documented justification and review.

**[MUST]** File upload processing: validate file type by content (magic bytes), not by extension. Uploaded files are served from a separate origin or with `Content-Disposition: attachment` to prevent execution.

---

## Transport

**[MUST]** TLS for all external communication. No plaintext HTTP in production.

**[MUST]** HSTS enabled on all production domains with a minimum max-age of one year.

**[SHOULD]** Certificate pinning for mobile applications communicating with Kornspire-controlled APIs.

---

## Dependencies

**[MUST]** Lock file committed and used in CI. `npm install` without a lock file produces non-reproducible builds.

**[MUST]** Automated dependency vulnerability scanning in CI (GitHub Dependabot, `npm audit`, Snyk, or equivalent). A failing audit does not automatically block the build, but it does require a documented decision to proceed.

**[SHOULD]** Dependencies reviewed before addition: what does this package do, what are its own dependencies, is it maintained, what is its licence?

**[MUST]** No dependencies whose licence is incompatible with the project's distribution model (AGPL in a proprietary application, for example, unless the client has reviewed and accepted).

---

## OWASP Top 10 review

Before any production deployment, the relevant items from the current OWASP Top 10 are reviewed for the system being deployed. This is not a checkbox exercise — it is a question asked of the specific system: "How could an attacker exploit this class of vulnerability here, and what have we done to prevent it?"

The review is documented. "Not applicable" is a valid finding with a reason.

---

## Data minimisation

**[MUST]** Collect only the personal data required for the stated purpose. If the feature does not need the user's phone number, do not collect it.

**[SHOULD]** Data retention policy documented before the system goes to production. Data that is no longer needed for its stated purpose is deleted, not archived indefinitely.

**[MUST]** Personal data not in logs. User IDs (pseudonymous identifiers) are acceptable. Names, email addresses, appointment details, and payment data are not.
