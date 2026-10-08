# Pattern: Multi-Tenant SaaS Architecture

This document describes the architecture patterns Kornspire applies when building multi-tenant SaaS products. The decisions behind data isolation specifically are recorded in [ADR-004](../decisions/ADR-004-multi-tenant-saas-data-isolation.md).

---

## Tenant resolution

Every request must resolve to a tenant before any business logic executes. The resolution mechanism depends on how the product is accessed:

**Subdomain routing** (`acme.platform.com`): Extract the subdomain from the `Host` header, resolve to a tenant record, reject unknown subdomains with 404. Do not use the URL path (`platform.com/acme`) for tenant identification when the product has per-tenant custom domains — subdomains compose better.

**Authentication-first** (a single domain, tenant determined by the authenticated user): Resolve the tenant from the user's session after authentication. Suitable for internal tools and B2B products where users only ever belong to one tenant.

**Hybrid** (authentication-first with optional custom domain): Most common for product SaaS. The platform domain authenticates the user and determines tenant; a tenant may optionally have a custom domain that bypasses authentication for public-facing pages (booking widgets, public profiles).

**Never** resolve the tenant from a query parameter or a request header the client controls. Both are trivially spoofed.

---

## Authentication and session isolation

- Authentication is per-tenant: a user belonging to tenant A cannot authenticate into tenant B, even with valid credentials for both. The session token encodes the tenant.
- Session tokens contain the tenant identifier as a signed claim, not a mutable field. The application layer reads the tenant from the token, not from a request parameter.
- Password reset, magic link, and OAuth flows must validate that the identity being authenticated belongs to the tenant the flow was initiated from. Cross-tenant account takeover via password reset is a real vulnerability class.

---

## Data isolation

See [ADR-004](../decisions/ADR-004-multi-tenant-saas-data-isolation.md) for the rationale behind schema-per-tenant isolation.

**Schema provisioning:** When a new tenant is created, the provisioning flow:

1. Creates the tenant record in the platform's shared tables
2. Creates the tenant schema in the database
3. Runs all migrations against the new schema
4. Runs any seed data required for the tenant to operate (default settings, system records)
5. Only marks the tenant as active after all steps succeed

Provisioning failures should leave no partial state. If any step fails, the created schema is dropped and the tenant record is not marked active.

**Connection management:** The database connection pool sets the schema `search_path` at checkout, not at application startup. A misconfigured connection must not fall through to the default schema or to another tenant's schema — if the `search_path` cannot be set, the checkout fails.

---

## Feature flags and configuration per tenant

Multi-tenant products frequently need to enable features for some tenants before rolling out to all. Use a tenant-level feature flag store rather than code branches.

The flag store should support:
- Boolean flags (feature on/off per tenant)
- Graduated rollouts (percentage of tenants)
- Tier-based flags (features available to paid plans but not free)

Do not use feature flags to maintain permanently divergent code paths. A flag that has been on for all tenants for six months should be removed from the code.

---

## Tenant-aware logging and monitoring

Logs must include the tenant identifier on every event. Without it, debugging a production issue requires reconstructing which tenant was affected from other signals — which is slow and error-prone.

**Do not log personal data in application logs.** Log the tenant ID, the user ID (a pseudonymous identifier), the operation, and the outcome. Never log the user's name, email, appointment details, or payment data.

Monitoring should support per-tenant alerting for error rate spikes. A single large tenant with a misconfigured integration can produce a noise floor that masks real issues for other tenants.

---

## Billing and subscription enforcement

- Enforce plan limits at the API layer, not the UI layer. The UI communicating "you have reached your limit" does not prevent a determined user from calling the API directly.
- Enforce limits synchronously for operations that have immediate cost (sending an SMS, placing a charge). Enforce limits asynchronously for operations where eventual consistency is acceptable (storage quota checks).
- Record the plan at the time of each billed operation, not the current plan. Plan downgrades should not retroactively invalidate existing records.

---

## Data export and tenant offboarding

Every tenant must be able to export their own data in a portable format. This is both a legal obligation in most jurisdictions and a trust signal.

Tenant offboarding should:
1. Prevent new logins immediately on cancellation
2. Preserve data for the legally required retention period
3. Provide a data export within a defined SLA
4. Delete data after the retention period expires, with a logged audit trail

Do not design the system such that tenant data deletion requires manual database access.
