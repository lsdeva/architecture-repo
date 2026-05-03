# Data & Analytics

Systems whose primary purpose is to store, transform, analyse, or visualise data — including modern ML training pipelines.

## When to look here

- The primary value is reporting, dashboards, ML model training, or data products
- Volumes are too large for a transactional database to handle efficiently
- Multiple source systems need to be integrated for analysis

## Patterns in this category

| Pattern | Status | Best for |
|---|---|---|
| [Modern Data Stack (Warehouse + dbt + BI)](modern-data-stack.md) | Adopt | Standard analytics needs; most companies under 1k employees |
| [Lakehouse](lakehouse.md) | Trial | Mixed BI + ML workloads at scale |
| [Real-time Streaming Analytics](real-time-streaming-analytics.md) | Trial | Sub-minute latency dashboards, fraud detection |
| [ML Training Pipeline](ml-training-pipeline.md) | Trial | Recurring model retraining with versioned data and experiments |
| [Embedded Analytics](embedded-analytics.md) | Adopt | Showing dashboards inside a SaaS product |

## Default tech stack

- **Warehouse:** Snowflake, BigQuery, Redshift, or DuckDB (for small teams)
- **Transformation:** dbt (the de-facto standard)
- **Orchestration:** Airflow, Dagster, Prefect
- **BI:** Metabase (open source), Looker, Tableau, Superset
- **ML platform:** Databricks, SageMaker, Vertex AI, or self-hosted MLflow + Ray

## LLM-development fit

Strong for SQL and pandas-style code; weaker on judgement (which join is right, which feature matters). Good for boilerplate, risky for correctness without verification.
