# Backend & Distributed Systems

Long-running services with no primary UI of their own. They expose APIs, process events, coordinate workflows, and integrate other systems.

## When to look here

- The system is consumed primarily by other systems, not directly by end users
- Multiple services need to coordinate via events or messages
- Throughput, consistency, or availability requirements exceed what a monolith can provide

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [Modular Monolith](modular-monolith.md) | Adopt | Default starting point; most "microservices" projects should be this |
| [Microservices](microservices.md) | Trial | Large engineering organisations with proven need to scale teams |
| [Event-Driven Architecture](event-driven-architecture.md) | Trial | Asynchronous workflows, decoupling producers from consumers |
| [API Gateway + BFF](api-gateway-bff.md) | Adopt | Many client types (web, mobile, partners) sharing backend services |
| [Workflow Orchestration](workflow-orchestration.md) | Trial | Long-running, human-in-the-loop, or failure-prone processes |

## Default tech stack

- **Languages:** Go, Java (Spring Boot), .NET, Node (NestJS)
- **Communication:** REST + JSON (default), gRPC (internal high-throughput), Kafka or NATS (events)
- **Service mesh:** Istio or Linkerd, only when service count exceeds ~20
- **Workflow engine:** Temporal (the modern default)
- **Observability:** OpenTelemetry → Datadog or Grafana stack

## LLM-development fit

Mixed. Individual services are well-handled; cross-cutting concerns (consistency, idempotency, security boundaries, failure modes) need careful human review.
