# Architecture Decision Records

Architecture Decision Records (ADRs) capture significant choices and **why** we made them. Each ADR is short, dated, and immutable — when a decision is reversed, the new ADR supersedes the old one rather than editing it in place.

We use the [Michael Nygard format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions): Context → Decision → Alternatives → Consequences.

## Index

| ID | Title | Status | Date |
|---|---|---|---|
| [ADR-0001](0001-tenant-isolation-strategy.md) | Tenant isolation via PostgreSQL Row-Level Security | Accepted | 2026-05-02 |
| [ADR-0002](0002-default-scripting-language.md) | Python as the default scripting language | Accepted | 2026-05-02 |
| [ADR-0003](0003-etl-orchestrator.md) | Prefect as the default ETL/ELT orchestrator | Accepted | 2026-05-02 |
| [ADR-0004](0004-vector-store.md) | pgvector as the default vector store | Accepted | 2026-05-02 |
| [ADR-0005](0005-llm-orchestration.md) | LLM orchestration — hand-rolled over frameworks for most patterns | Accepted | 2026-05-02 |
| [ADR-0006](0006-llm-provider.md) | Anthropic Claude as the default LLM provider | Accepted | 2026-05-02 |
| [ADR-0007](0007-lakehouse-table-format.md) | Delta Lake as the default lakehouse table format | Accepted | 2026-05-03 |
| [ADR-0008](0008-modular-monolith-default.md) | Modular Monolith as the default starting architecture | Accepted | 2026-05-03 |
| [ADR-0009](0009-workflow-engine.md) | Temporal as the default workflow orchestration engine | Accepted | 2026-05-03 |
| [ADR-0010](0010-game-engine.md) | Game engine selection policy (Godot default, Unity / Unreal criteria) | Accepted | 2026-05-03 |
| [ADR-0011](0011-systems-language.md) | Rust as the default language for new systems and infrastructure code | Accepted | 2026-05-03 |

## Writing a new ADR

1. Copy `_template/adr-template.md` to `docs/decisions/NNNN-short-title.md` (next sequential number)
2. Fill it in
3. Open a pull request — at least one other architect must review
4. Once merged, link it from any affected pattern under the "Related decisions" section
