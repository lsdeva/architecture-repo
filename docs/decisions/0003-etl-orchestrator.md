# ADR-0003: Prefect as the default ETL/ELT orchestrator

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** etl, orchestration, data, scheduling

## Context

The ETL/ELT job pattern needs a default orchestrator recommendation. The choice determines: how pipelines are defined (code vs YAML/DAG), how they are monitored, how failures are retried, and how much infrastructure the data team must operate. The options range from "nothing" (plain cron) to full-featured platforms (Airflow, Prefect, Dagster). This choice is expensive to reverse because pipelines accumulate and migration is manual.

The realistic candidates for a team starting a new data platform in 2026 are Apache Airflow (the incumbent), Prefect (Python-native, cloud-hosted control plane), Dagster (asset-oriented), and plain cron + systemd (no framework). Spark and Luigi are excluded as they address different scales or are effectively deprecated.

## Decision

We will use **Prefect** as the default orchestrator for ETL/ELT jobs.

Plain `cron` (via GitHub Actions scheduled runs or AWS EventBridge) remains acceptable for single-step jobs that do not need retry logic, dependency chaining, or a UI.

## Alternatives considered

### Option A: Apache Airflow
- **Pros:** Industry standard — the largest community, most connectors, and broadest employer recognition. Rich plugin ecosystem. Kubernetes Executor scales workers horizontally.
- **Cons:** Operationally heavy even on managed offerings (MWAA costs ~$300/month minimum; Astronomer is more). DAGs are defined as Python but in a constrained execution model (no local state, no direct function calls in task bodies) that surprises engineers. Scheduler and webserver are separate processes requiring high availability configuration. Backfill and dependency semantics are complex to learn. Poor local development experience.

### Option B: Prefect (chosen)
- **Pros:** Pure Python — a flow is a regular Python function decorated with `@flow`; no DAG construction boilerplate. Excellent local development story (`prefect server start` runs the full UI locally in one command). Prefect Cloud free tier (3 workspaces, unlimited runs) covers small-to-medium data teams at zero cost. Automatic retry, caching, and logging with minimal configuration. Workers can run as Docker containers, ECS tasks, or local processes with no config change. `prefect.yaml` deployment config is version-controllable.
- **Cons:** Smaller community than Airflow. The Prefect 2.x → 3.x migration broke some APIs — verify the version before starting. Fewer pre-built integrations than Airflow's provider packages (though most are one `pip install prefect-aws` away). No built-in dbt-native DAG dependency graph (Dagster has this).

### Option C: Dagster
- **Pros:** Asset-oriented model (Software-Defined Assets) is an excellent fit for ELT patterns where the output is a table or dataset, not a task completion. First-class dbt integration. Built-in data quality checks via asset checks. Partitions and backfills are first-class concepts.
- **Cons:** Steeper learning curve than Prefect — the asset/job/op distinction takes time to internalise. Dagster Cloud (managed) is more expensive than Prefect Cloud. Smaller community than Airflow. The asset model is genuinely better for large data platforms but adds unnecessary complexity for simple ETL jobs.

### Option D: Plain cron (GitHub Actions or EventBridge)
- **Pros:** Zero new infrastructure. Every engineer already understands cron syntax. GitHub Actions scheduled workflows are free up to the minute budget and require no extra deployment.
- **Cons:** No retry logic without writing it yourself. No cross-job dependency management. No centralised monitoring UI. Failures surface only as GitHub notification emails unless you wire up alerting explicitly. Does not scale gracefully beyond ~5 independent jobs before operational visibility degrades.

## Consequences

**Positive**

- Python-native flows are readable by any Python engineer on the team, not just data engineers
- Prefect Cloud free tier eliminates infrastructure cost for small-to-medium data teams
- Local development is fast: run the same flow locally or in production without code changes
- Built-in retry, timeout, and notification blocks reduce boilerplate in every job

**Negative / trade-offs**

- Committing to Prefect means all flows use its `@flow` / `@task` decorators — migration to another orchestrator means rewriting every pipeline entry point
- Prefect's API has had breaking changes between major versions; pin the version in `pyproject.toml` and allocate time to upgrade
- For large data platforms (> 50 pipelines, asset lineage requirements), Dagster's asset model becomes more valuable and the switch becomes worth the migration cost

**Follow-up actions**

- [ ] Add `prefect-aws` to the standard ETL Docker base image
- [ ] Create a shared `prefect.yaml` deployment template in the internal tooling repo
- [ ] Define the threshold for switching to Dagster (proposed: > 50 active pipelines or when dbt asset lineage visibility becomes a requirement)
- [ ] Configure Prefect Cloud workspace and document the on-call alert webhook setup
