# Architecture Decision Records

Architecture Decision Records (ADRs) capture significant choices and **why** we made them. Each ADR is short, dated, and immutable — when a decision is reversed, the new ADR supersedes the old one rather than editing it in place.

We use the [Michael Nygard format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions): Context → Decision → Alternatives → Consequences.

## Index

| ID | Title | Status | Date |
|---|---|---|---|
| [ADR-0001](0001-tenant-isolation-strategy.md) | Tenant isolation via PostgreSQL Row-Level Security | Accepted | 2026-05-02 |

## Writing a new ADR

1. Copy `_template/adr-template.md` to `docs/decisions/NNNN-short-title.md` (next sequential number)
2. Fill it in
3. Open a pull request — at least one other architect must review
4. Once merged, link it from any affected pattern under the "Related decisions" section
