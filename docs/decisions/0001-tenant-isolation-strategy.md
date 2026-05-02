# ADR-0001: Tenant isolation via PostgreSQL Row-Level Security

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** database, security, multi-tenancy

## Context

Our SaaS multi-tenant pattern needs to isolate tenant data so that a bug in application code cannot accidentally leak one tenant's data to another. Three common approaches exist, each with very different operational and cost profiles.

## Decision

We will use **PostgreSQL Row-Level Security (RLS)** with a `tenant_id` column on every tenant-scoped table, combined with a session variable set on every connection from the application layer.

## Alternatives considered

### Option A: Row-level (shared schema, `tenant_id` column)
- **Pros:** Cheapest infrastructure, simplest backups and migrations, easiest cross-tenant analytics, supports the largest number of tenants per dollar.
- **Cons:** A buggy query without a tenant filter is a data breach. RLS mitigates but does not eliminate this. Single noisy tenant can affect others.

### Option B: Schema-per-tenant
- **Pros:** Stronger isolation by default; per-tenant restore is easier.
- **Cons:** Migrations must be applied N times. Postgres does not love thousands of schemas (system catalogue bloat). Cross-tenant analytics is painful.

### Option C: Database-per-tenant
- **Pros:** Strongest isolation. True per-tenant residency, sizing, and backups. Required for some compliance regimes.
- **Cons:** Highest cost. Most operational overhead. Connection pool fragmentation. Generally only viable above a certain ARPU threshold or with dedicated single-tenant deployments.

## Consequences

**Positive**

- Lowest operational and infrastructure cost
- Simplest migration and deployment story
- Cross-tenant analytics queries are trivial
- Compatible with managed Postgres offerings (RDS, Cloud SQL, Supabase)

**Negative / trade-offs**

- Every developer must understand RLS — onboarding cost
- A linter rule is required to prevent queries that bypass RLS via `BYPASSRLS` roles
- Per-tenant restore requires `pg_dump --table` filtering, not a clean restore
- Hard limit somewhere between 5,000-10,000 active tenants on a single primary; sharding plan needed before then

**Follow-up actions**

- [ ] Document RLS policy template in the engineering wiki
- [ ] Add SQL linter (e.g., `squawk`) rule to CI pipeline
- [ ] Define the sharding trigger and target architecture for ADR-0002
- [ ] Tag every log line with `tenant_id` (see ADR-0003)
