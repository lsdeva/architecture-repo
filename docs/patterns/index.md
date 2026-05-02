# Patterns Overview

This catalog organises every solution architecture into one of eight categories. Each category contains multiple concrete patterns.

## Choosing the right category

If you are unsure which category your project belongs to, work through these questions in order:

1. **Is human safety or regulatory certification at stake?** → [Safety-Critical](06-safety-critical/index.md)
2. **Is the primary value an LLM/AI capability?** → [AI / LLM-Integrated](07-ai-llm-integrated/index.md)
3. **Is it interactive entertainment or real-time 3D?** → [Games & Graphics](08-games-graphics/index.md)
4. **Is it a one-shot or recurring data transformation?** → [Scripts & Automation](02-scripts-automation/index.md)
5. **Is the primary value reporting, analytics, or ML training?** → [Data & Analytics](03-data-analytics/index.md)
6. **Is it low-level (kernel, driver, embedded, database engine)?** → [Systems & Infrastructure](05-systems-infra/index.md)
7. **Does it expose APIs to many other services without a primary UI?** → [Backend & Distributed Systems](04-backend-distributed/index.md)
8. **Anything else with a user-facing UI** → [Web & Mobile Applications](01-web-mobile/index.md)

## Maturity legend

Patterns are rated using the Thoughtworks Tech Radar scheme:

| Ring | Meaning |
|---|---|
| **Adopt** | Proven in production. Default choice. |
| **Trial** | Use with confidence on appropriate projects. |
| **Assess** | Worth a pilot. Understand the trade-offs first. |
| **Hold** | Avoid for new projects. Existing usage acceptable. |
