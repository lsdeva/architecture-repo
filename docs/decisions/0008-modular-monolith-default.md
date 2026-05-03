# ADR-0008: Modular Monolith as the default starting architecture

- **Status:** Accepted
- **Date:** 2026-05-03
- **Deciders:** Architecture Team
- **Tags:** architecture, backend, microservices, monolith

## Context

Every new backend system must start somewhere. The two common starting points are a monolith (one deployable, one codebase) and microservices (multiple deployables, multiple codebases). The industry spent most of the 2010s defaulting to microservices on greenfield projects and accumulated a large body of experience showing the consequences: teams of 3–5 engineers operating 15 services, distributed tracing from day one, and months spent on platform engineering before shipping the first user-facing feature.

This ADR records the default starting point for new backend systems in this organisation and the criteria for deviating from it.

The decision is non-obvious because microservices are widely associated with "modern" architecture, and choosing a monolith can feel like choosing to incur technical debt. The evidence suggests the opposite is true when service boundaries are premature.

## Decision

We will default to a **Modular Monolith** for all new backend systems.

We will consider extracting a service from the monolith when **all three** of the following conditions are met:
1. A specific module has a measured, proven scaling requirement that cannot be met by vertically or horizontally scaling the whole monolith
2. A named, independent product team will own the extracted service end-to-end
3. The module boundary has been stable for at least 3 months in the monolith (unstable boundaries make service extraction expensive)

We will **not** start with microservices because:
- Domain boundaries on a new system are hypotheses, not facts
- Platform overhead (K8s, distributed tracing, multiple CI pipelines) on a small team is a productivity tax with no current benefit
- Microservices can always be extracted later; they cannot be easily collapsed back

## Alternatives considered

### Option A: Modular Monolith (chosen)
- **Pros:** Single deployment, single database, single CI pipeline, easy to debug (one process, one log stream). Module boundaries enforced in code give the architectural clarity of microservices without the operational complexity. Extraction is always available as a later option when genuine need is proven. Fast iteration: a cross-module change is one PR, not five.
- **Cons:** The whole monolith must be deployed to change any module — deployment conflicts between teams are possible at larger team sizes. The monolith cannot scale one module independently of others (within the same deployment unit). Risk of module boundaries eroding over time if not enforced by tooling or code review.

### Option B: Microservices from day one
- **Pros:** Teams can deploy independently from day one. Each service scales independently. Technology heterogeneity is possible per service.
- **Cons:** Requires stable domain boundaries — which do not exist on a new system. Forces distributed systems complexity (network failures, eventual consistency, distributed tracing) before any business value is delivered. Platform engineering team required before development can begin. Martin Fowler's "microservice premium": only pays off when the system is complex enough that the premium is worth it. Sam Newman's recommendation: start with a monolith unless you already have a very clear domain model.

### Option C: "Macroservices" (2–4 coarse-grained services)
- **Pros:** Some team autonomy without fine-grained complexity.
- **Cons:** Arbitrary decomposition without proven boundaries produces the worst of both worlds: inter-service calls with no scaling benefit. Not recommended.

## Consequences

**Positive**

- New backend systems start delivering user value faster (no platform bootstrapping required)
- Domain boundaries emerge from real usage rather than being guessed upfront
- When extraction eventually happens, the module boundary in the monolith becomes the service interface — less rewriting
- Simpler debugging, simpler deployments, lower infrastructure cost at early scale

**Negative / trade-offs**

- Teams must enforce module boundaries actively in code review — the monolith will erode toward a "big ball of mud" if discipline lapses
- At some scale (> 10 independent teams), the modular monolith becomes the bottleneck — the extraction criteria above must be applied proactively, not reactively
- Engineers joining from microservices-heavy organisations may need to adjust expectations

**Follow-up actions**

- [ ] Add a linting rule that detects cross-module direct imports (e.g., `eslint-plugin-boundaries` for NestJS, `depguard` for Go) to the standard project template
- [ ] Document the extraction criteria in the engineering onboarding guide
- [ ] Schedule a quarterly review of any module that is flagged as a potential extraction candidate
