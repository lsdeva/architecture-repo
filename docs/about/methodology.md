# Methodology

This catalog combines several established practices into a single workflow.

## Standards we borrow from

- **[C4 Model](https://c4model.com/)** by Simon Brown — for diagram conventions. Every pattern shows at least a System Context (Level 1) and Container (Level 2) view.
- **[Arc42](https://arc42.org/)** — for the structure of long-form architecture documents (we use a condensed subset).
- **[Architecture Decision Records (ADRs)](https://adr.github.io/)** in the Michael Nygard format — for capturing decisions and their rationale.
- **[Thoughtworks Tech Radar](https://www.thoughtworks.com/radar)** — for the Adopt / Trial / Assess / Hold maturity ratings.
- **[ISO/IEC/IEEE 42010](https://www.iso.org/standard/74393.html)** — the international standard for architecture description; informs the multi-stakeholder framing.

## Why a category-first organisation?

Architects generally know within minutes of hearing a brief which broad family of system they will build. Categorising first lets them jump directly to the relevant patterns, rather than browsing a flat list of dozens of unrelated entries.

The eight categories here are pragmatic, not theoretically pure. Some systems span categories (a mobile app with a heavy LLM feature, say). In those cases, treat the pattern most central to the value as the primary category and link the secondary patterns from the project document.

## Why include LLM-development fit ratings?

In 2026, "can AI assistants help build this?" is a relevant cost and timeline factor. The ratings reflect our experience and the public reports of practitioners (Simon Willison, Armin Ronacher, Addy Osmani and others). They are opinionated and may change quickly as tooling improves.

## Review cadence

- **Every pattern** is reviewed at least once every 12 months.
- **AI/LLM-Integrated patterns** are reviewed every 6 months — this category moves fastest.
- **ADRs** are immutable. When a decision changes, write a new ADR that supersedes the old one.
