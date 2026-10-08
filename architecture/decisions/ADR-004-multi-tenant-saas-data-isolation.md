# ADR-004: Multi-Tenant SaaS Data Isolation Strategy

**Status:** Accepted  
**Date:** 2026-09-23  
**Context:** Derived from architectural decisions made while building Upkilo, Kornspire's own multi-tenant SaaS platform for appointment-based businesses

---

## Context

Upkilo is a multi-tenant SaaS platform. Each tenant — a salon, clinic, spa, or similar business — has its own data: appointments, clients, staff, payments, communications. No tenant may see another's data under any circumstances.

At the time of the architectural decision, we needed to choose a data isolation strategy that would:

- Prevent cross-tenant data leakage under both correct operation and failure conditions
- Support independent migration of tenant schemas without a maintenance window on the entire platform
- Scale to hundreds of tenants on a shared database infrastructure without proportional cost
- Not require a separate database server per tenant at the scale we expected to reach in year one

This ADR records that decision, the alternatives we considered, and the reasoning. It is documented here because multi-tenant data isolation is one of the decisions that is expensive to reverse — the right time to evaluate it is before the first row of data is written.

---

## Options considered

### Option 1: Row-level isolation (shared tables, `tenant_id` column)

Every table has a `tenant_id` column. Application code filters by the authenticated tenant's ID on every query. Some databases support row-level security (RLS) policies to enforce this at the database layer.

**Advantages:**
- Simplest schema — one set of tables, easy to query across tenants for platform analytics
- No per-tenant provisioning overhead
- Schema migrations apply to all tenants at once

**Disadvantages:**
- Application-layer enforcement is fragile: a single missing `WHERE tenant_id = ?` clause exposes all tenants' data to the authenticated tenant. This has happened in production systems at scale.
- RLS policies mitigate but do not eliminate this risk: RLS must be correctly configured per table, per operation, and policy changes require testing across all tenants simultaneously.
- A bug in the query layer does not fail loudly — it silently returns wrong data
- Backup and restore for a single tenant requires extracting rows from shared tables

### Option 2: Schema-per-tenant (one schema per tenant, shared database server)

Each tenant gets a dedicated schema within a shared PostgreSQL database. Application code connects to the shared server but sets the `search_path` to the tenant's schema at the start of each session.

**Advantages:**
- Tenant data is physically separated at the schema level — a query cannot accidentally return another tenant's data because the tables themselves are different objects
- Per-tenant backup and restore is straightforward
- Schema migrations can be applied per-tenant, enabling gradual rollout and easier rollback for individual tenants
- Moving a tenant to a dedicated server is a clean schema dump and restore

**Disadvantages:**
- More complex provisioning: creating a tenant requires creating a schema and running migrations against it
- Cross-tenant queries (platform analytics) require explicit cross-schema joins or a separate reporting layer
- Schema count grows with tenant count; at very large scale (thousands of tenants) this can affect PostgreSQL's schema cache

### Option 3: Database-per-tenant (separate database server per tenant)

Each tenant has an entirely separate database server (or database instance on a managed service).

**Advantages:**
- Strongest isolation: no shared infrastructure between tenants
- Resource contention is impossible — a large tenant cannot starve a small one
- Compliance and audit surface is reduced per tenant

**Disadvantages:**
- Operational cost scales directly with tenant count: each new tenant requires provisioning a new database instance
- At managed service prices (Azure Database for PostgreSQL Flexible Server), the fixed cost per tenant makes small tenants economically unviable
- Schema migrations across all tenants require orchestration infrastructure
- Monitoring and alerting complexity grows proportionally

---

## Decision

**Schema-per-tenant** within a shared PostgreSQL database.

**Reasoning:**

The fundamental failure mode of row-level isolation is silent data leakage. A missing filter condition returns wrong data without an error, without a log entry, and often without immediate user detection. For a platform handling appointment, client, and payment data, this failure mode is categorically unacceptable. The technical convenience of a simpler schema does not justify the risk.

Database-per-tenant provides stronger isolation but at a cost structure that does not work for a SaaS targeting small businesses: the fixed cost of a managed database instance per tenant would make the economics unworkable at the tenant sizes we serve.

Schema-per-tenant provides the isolation guarantees we need — a query cannot reach another tenant's data without an explicit cross-schema reference — while remaining operationally practical on a single managed database server. The provisioning overhead is manageable with automation, and the migration story (apply per tenant, verify, continue) is strictly better than all-at-once for a production platform where a bad migration cannot cause a total outage.

---

## Implementation notes

- Tenant schemas are provisioned by the platform when a new account is created, not manually
- The application resolves the current tenant from the authentication context, not from a URL segment or request header that could be spoofed
- `search_path` is set at connection checkout, not at application startup
- Platform-level analytics that require cross-tenant data use a separate reporting schema populated by scheduled jobs, not live cross-schema queries
- Migrations run per tenant via the deployment pipeline; a failed migration on one tenant is isolated and does not block others

---

## When to choose differently

| Situation | Recommendation |
|---|---|
| Tens of large enterprise tenants, each willing to pay for dedicated infrastructure | Database-per-tenant |
| Internal tool with a small known set of tenants and no PII | Row-level isolation is acceptable |
| Regulated industry requiring complete infrastructure isolation | Database-per-tenant; the compliance cost justifies the operational cost |
| Tenant count expected to exceed thousands on a single server | Evaluate sharding strategy before committing to schema-per-tenant |
