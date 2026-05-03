# Web & Mobile Applications

User-facing applications delivered through browsers or mobile app stores. The largest and most diverse category — covers everything from a marketing site to a multi-tenant SaaS platform.

## When to look here

- The end user interacts with a UI (web, mobile, or both)
- Most logic can live behind a request/response API
- Acceptable latency is hundreds of milliseconds, not microseconds

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [SaaS Multi-Tenant Web App](saas-multi-tenant.md) | Adopt | B2B subscription products, internal platforms with multiple customers |
| [Static Marketing Site](static-marketing-site.md) | Adopt | Brochure sites, landing pages, documentation portals |
| [E-commerce Storefront](ecommerce-storefront.md) | Adopt | Online retail, marketplaces |
| [Internal Admin / Back-office Tool](internal-admin-tool.md) | Adopt | CRMs, dashboards, ops consoles |
| [Cross-platform Mobile App](cross-platform-mobile-app.md) | Trial | Apps needing iOS + Android with shared codebase |
| [Progressive Web App (PWA)](progressive-web-app.md) | Trial | Installable web apps with offline support |

## LLM-development sweet spot

This is the category where AI-assisted development pays off the most. Patterns are heavily represented in training data, frameworks are mature, and the work is mostly composition rather than novel algorithm design.
