# Architecture Pattern Catalog

A curated reference library of solution architectures, organised by software type. Use it to **shortcut the planning phase** when a new project lands on your desk.

## How this catalog is organised

Every IT solution we have ever built fits into one of eight broad families. For each family we maintain reference patterns covering the typical sub-types, complete with technology stacks, diagrams, sizing, non-functional characteristics, and decision rationale.

| # | Category | Examples |
|---|---|---|
| 1 | [Web & Mobile Applications](patterns/01-web-mobile/index.md) | SaaS, e-commerce, admin portals, mobile apps |
| 2 | [Scripts & Automation](patterns/02-scripts-automation/index.md) | ETL jobs, CLIs, scrapers, CI/CD pipelines |
| 3 | [Data & Analytics](patterns/03-data-analytics/index.md) | Warehouses, BI, ML pipelines, lakehouses |
| 4 | [Backend & Distributed Systems](patterns/04-backend-distributed/index.md) | Microservices, event-driven, payments, search |
| 5 | [Systems & Infrastructure](patterns/05-systems-infra/index.md) | Databases, OS components, embedded firmware |
| 6 | [Safety-Critical](patterns/06-safety-critical/index.md) | Medical, avionics, automotive, industrial control |
| 7 | [AI / LLM-Integrated](patterns/07-ai-llm-integrated/index.md) | Chatbots, RAG, agents, copilots |
| 8 | [Games & Graphics](patterns/08-games-graphics/index.md) | Game engines, simulations, 3D rendering |

## How to use it

1. **Identify the category** the new requirement falls into.
2. Open the **category index** to see all patterns inside it.
3. Pick the pattern that matches the project shape; copy its tech stack and diagrams as your starting baseline.
4. Read the linked **Architecture Decision Records (ADRs)** to understand the trade-offs already considered.
5. Adjust for the client's specific constraints — and if the deviation is significant, write a new ADR.

See [How to use this catalog](about/how-to-use.md) for the full walkthrough.

## Maintenance promise

Every pattern carries a "Last reviewed" date. Anything older than 12 months should be treated as suspect — the technology landscape moves fast, especially in the AI/LLM-integrated category.
