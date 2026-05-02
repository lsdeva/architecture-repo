# Scripts & Automation

Short-lived or recurring code that transforms data, glues systems together, or automates tasks. Often single-author, often single-file.

## When to look here

- The deliverable is a script or job, not a long-running service
- It runs on a schedule, on demand, or as part of a CI/CD pipeline
- It is acceptable for the script to be re-run if it fails

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [ETL/ELT Job](etl-elt-job.md) | Adopt | Moving data between systems on a schedule |
| [Web Scraper](web-scraper.md) | Adopt | Pulling structured data from public web sources |
| [CLI Tool](cli-tool.md) | Adopt | Internal developer tooling, ops automation |
| [Serverless Function](serverless-function.md) | Adopt | Event-driven glue (webhook handlers, file processors) |
| [CI/CD Pipeline](cicd-pipeline.md) | Adopt | Build, test, deploy automation |

## Default tech stack

- **Language:** Python (default), Go (for distribution as a single binary), Bash (under 50 lines only)
- **Scheduling:** GitHub Actions cron, AWS EventBridge, Temporal (for complex workflows)
- **Packaging:** `uv` for Python projects, single static binary for Go
- **Storage:** SQLite for local state, Postgres for shared state, S3 for blob output

## LLM-development sweet spot

Highest-leverage category for AI-assisted development. Self-contained, easy to test by running, low blast radius for mistakes.
