# ADR-0009: Temporal as the default workflow orchestration engine

- **Status:** Accepted
- **Date:** 2026-05-03
- **Deciders:** Architecture Team
- **Tags:** backend, workflow, orchestration, temporal

## Context

The Workflow Orchestration pattern requires choosing a durable execution engine. Several mature options exist with different programming models, operational profiles, and cloud dependencies. The choice is non-obvious and expensive to reverse because workflow definitions accumulate and migration requires rewriting every workflow and re-deploying in-flight executions.

The realistic candidates in 2026 for a team building application-level business workflows (not data pipelines) are Temporal, AWS Step Functions, and Apache Airflow. Cadence (Temporal's predecessor at Uber) is excluded as Temporal is its successor.

## Decision

We will use **Temporal** as the default orchestration engine for business workflow orchestration.

Apache Airflow remains the default for data pipeline orchestration (see ADR-0003).

AWS Step Functions is acceptable for simple, short-lived workflows that are tightly coupled to AWS services, with explicit sign-off from the architecture team.

## Alternatives considered

### Option A: Temporal (chosen)
- **Pros:** Code-first workflow definitions in Python, Go, Java, or TypeScript — workflows are regular functions decorated with the Temporal SDK, not JSON/YAML state machine definitions. Full replay semantics: Temporal persists every event in workflow history and can reconstruct state after any failure. First-class support for signals (external events resume a waiting workflow), timers, child workflows, and human-in-the-loop patterns. Local development is fast: `temporal server start-dev` runs a full server in one command with a UI. Temporal Cloud (managed) eliminates the operational burden. Open-source with a large community.
- **Cons:** Self-hosting Temporal Server requires operating Postgres and multiple Temporal services (frontend, history, matching, worker). Temporal Cloud adds a per-action cost. The replay determinism constraint (workflow code must be deterministic) is a non-obvious programming model that requires onboarding. Temporal history for very high-volume workflows (> 100k events per workflow) requires careful management with `ContinueAsNew`.

### Option B: AWS Step Functions
- **Pros:** Fully managed — no infrastructure to operate. Native AWS service integration (Lambda, DynamoDB, ECS, SNS) without writing activity code. Pay-per-state-transition pricing with a generous free tier for simple workflows. Express Workflows for high-volume short-duration workflows.
- **Cons:** Workflow definitions are JSON/YAML state machines — not code. Complex branching and error handling logic in JSON is verbose and hard to test. No replay semantics for state reconstruction — Standard Workflows log to CloudWatch, not a queryable workflow history. Vendor lock-in: deeply coupled to AWS; migrating away requires rewriting every workflow. Activity code must be Lambda functions — cold start latency applies. Poor developer experience compared to Temporal.

### Option C: Apache Airflow
- **Pros:** Large community. Excellent for data pipeline DAGs. Many operators for external systems (S3, Snowflake, dbt, Spark).
- **Cons:** Designed for data pipelines, not general business workflows. DAG scheduling model is not suited to event-triggered or human-in-the-loop workflows. No native support for Signals or durable waiting for external events. Heavyweight to self-host. The Temporal pattern team has evaluated Airflow for business workflows and found its execution model too restrictive.

### Option D: Netflix Conductor
- **Pros:** Designed for microservice workflow orchestration. JSON-based workflow definitions. REST API for all interactions.
- **Cons:** Significantly smaller community than Temporal. Orkes (the managed Conductor service) is less mature than Temporal Cloud. The programming model is less ergonomic than Temporal's code-first approach.

## Consequences

**Positive**

- Workflow code is testable with the Temporal `TestWorkflowEnvironment` (time-skip); no need to run a real Temporal server in CI
- Full workflow execution history is queryable from a single UI — debugging stuck workflows does not require trawling distributed logs
- Temporal's replay model gives exact-once semantics for free on activity retry — no need to implement idempotency keys in every activity
- Temporal Cloud eliminates the operational burden of running Temporal Server for teams that don't want to manage it

**Negative / trade-offs**

- The determinism constraint (no `time.Now()`, no random numbers, no direct I/O in workflow code) is non-obvious and will catch developers off guard the first time; requires explicit onboarding
- Self-hosting Temporal Server requires operating Postgres + 4 Temporal services with HA; commit to Temporal Cloud if the team cannot invest in this
- Temporal Cloud adds cost at scale (~$0.002 per workflow action); model this cost before committing for high-volume use cases
- Migrating in-flight workflows when changing the engine is not possible — existing workflows must run to completion on the old engine

**Follow-up actions**

- [ ] Publish a "Temporal determinism cheat sheet" in the engineering wiki: what is allowed in workflow code vs activity code
- [ ] Add a `TestWorkflowEnvironment`-based unit test template to the standard project scaffolding
- [ ] Evaluate Temporal Cloud vs self-hosted for the first production use case; document the cost model
- [ ] Define the standard namespace and retention policy for each environment (dev: 3 days, staging: 14 days, prod: 90 days)
