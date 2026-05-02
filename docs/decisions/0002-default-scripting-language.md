# ADR-0002: Python as the default scripting language

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** language, scripts, automation, cli, etl

## Context

The Scripts & Automation category covers ETL jobs, web scrapers, CLI tools, and serverless functions. All of these need a default language recommendation so that teams can share libraries, review conventions, and hire for one skill set rather than four. The realistic candidates are Python, Go, Bash, Node.js, and Ruby. The choice is non-obvious because Go has real advantages for CLI distribution and cold-start performance, and Bash is already present on every machine.

## Decision

We will use **Python 3.12+ with `uv`** as the default language for all scripts and automation workloads.

Go is explicitly recommended as the alternative when producing a distributable single binary for machines that may not have Python installed. Bash is acceptable for scripts under 50 lines with no error handling requirements.

## Alternatives considered

### Option A: Python (chosen)
- **Pros:** Richest data, HTTP, cloud, and ML library ecosystem. LLM code generation quality is highest. The widest pool of engineers on the team can read and modify it. `uv` solves the historical packaging pain with near-instant installs and lock files. First-class support in AWS Lambda, GCP Cloud Functions, Prefect, dbt, and Scrapy.
- **Cons:** Slower cold starts than Go (~300ms vs ~30ms for Lambda). Requires Python to be installed on the target machine for CLI tools. The GIL limits CPU parallelism within a single process (largely mitigated by async I/O for network-bound work).

### Option B: Go
- **Pros:** Single static binary with zero runtime dependency — ideal for CLI tools distributed to machines not running Python. Cold starts are 10× faster than Python on Lambda. Excellent concurrency model (goroutines) for I/O-bound scrapers and parallel ETL.
- **Cons:** Smaller data ecosystem (no pandas, no boto3 equivalents that match the Python versions in maturity). Longer initial development time for engineers not familiar with Go. LLM code generation quality is lower for domain-specific libraries. Generics arrived late (1.18, 2022) — some idioms are still verbose.

### Option C: Bash
- **Pros:** Zero dependencies, present on every Linux/macOS machine, excellent for gluing existing CLIs together.
- **Cons:** Error handling is notoriously fragile (`set -euo pipefail` helps but does not fix the fundamental model). No type system, no unit testing framework, poor readability beyond ~50 lines. POSIX portability problems on macOS vs Linux. Not viable for ETL, scraping, or anything with structured data.

### Option D: Node.js / TypeScript
- **Pros:** Strong async model, large npm ecosystem, natural choice if the team is frontend-heavy.
- **Cons:** npm dependency trees are famously large and fragile. No advantage over Python for data/cloud tasks. Adds a second runtime dependency alongside Python, which most backend teams already have.

## Consequences

**Positive**

- One language for all scripts — engineers can review each other's automation code without context switching
- `uv` produces a deterministic lock file; environments are reproducible across dev, CI, and Lambda
- AWS Lambda Powertools, dbt, Prefect, httpx, Playwright, and Typer are all Python-first libraries
- LLM-assisted development is most effective in Python; boilerplate generation is high quality

**Negative / trade-offs**

- Python Lambda cold starts require Provisioned Concurrency for latency-sensitive paths
- CLI tools built in Python require Python on the target machine; document this requirement or provide a `pipx install` one-liner
- CPU-bound parallel work within a single process requires `multiprocessing` or sub-processes (not threads) due to the GIL

**Follow-up actions**

- [ ] Add `uv` to the standard Docker base image for CI runners
- [ ] Document the `pipx install <tool>` pattern for distributing internal CLI tools
- [ ] Define the Go exception criteria: when a tool is distributed to > 10 non-Python machines, use Go
