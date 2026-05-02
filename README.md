# Architecture Pattern Catalog

A public, client-shareable reference library of solution architectures, organised by software type. Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

## Quick start

```bash
# Install dependencies (uses uv; pip works equally well)
pip install mkdocs-material pymdown-extensions

# Run a local dev server with live reload
mkdocs serve

# Build the static site
mkdocs build

# Deploy to GitHub Pages (one command)
mkdocs gh-deploy
```

The site will be at `http://localhost:8000` while `mkdocs serve` is running.

## Repository structure

```
architecture-repo/
├── mkdocs.yml                     # Site configuration
├── README.md                      # This file
├── _template/                     # Templates for new content
│   ├── pattern-template.md
│   └── adr-template.md
└── docs/
    ├── index.md                   # Home page
    ├── patterns/                  # Eight categories of architecture patterns
    │   ├── index.md               # Patterns overview + decision tree
    │   ├── 01-web-mobile/
    │   ├── 02-scripts-automation/
    │   ├── 03-data-analytics/
    │   ├── 04-backend-distributed/
    │   ├── 05-systems-infra/
    │   ├── 06-safety-critical/
    │   ├── 07-ai-llm-integrated/
    │   └── 08-games-graphics/
    ├── decisions/                 # Architecture Decision Records
    ├── diagrams/                  # Shared diagram source files (draw.io)
    ├── assets/                    # Images, logos, downloads
    └── about/                     # Methodology, how-to-use, contributing
```

## Authoring a new pattern

1. Decide which of the eight categories it belongs to
2. Copy `_template/pattern-template.md` into that category folder
3. Rename to a kebab-case filename (e.g. `event-driven-microservices.md`)
4. Fill in every section
5. Add an entry to that category's `index.md` table
6. If the pattern relies on a non-obvious decision, also write an ADR

## Authoring a new ADR

1. Find the next sequential number (look in `docs/decisions/`)
2. Copy `_template/adr-template.md` to `docs/decisions/NNNN-short-title.md`
3. Fill it in
4. Add an entry to `docs/decisions/index.md`
5. Link it from any affected pattern under "Related decisions"

## Deployment

The site is static HTML; deploy anywhere. Easiest options:

- **GitHub Pages:** `mkdocs gh-deploy` (one command, free)
- **Cloudflare Pages / Netlify / Vercel:** point at the repo, build command `mkdocs build`, output dir `site/`
- **Any static host:** upload the contents of `site/` after `mkdocs build`

## Conventions

- Diagrams in [Mermaid](https://mermaid.js.org/) by default (renders natively, version-controlled)
- ADRs in [Michael Nygard format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- Architecture diagrams follow the [C4 Model](https://c4model.com/) (Levels 1 and 2 minimum)
- Maturity ratings follow the [Thoughtworks Tech Radar](https://www.thoughtworks.com/radar) scheme

## License

Content licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) unless otherwise noted. Code samples are MIT licensed.
