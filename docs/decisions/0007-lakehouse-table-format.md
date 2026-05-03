# ADR-0007: Delta Lake as the default lakehouse table format

- **Status:** Accepted
- **Date:** 2026-05-03
- **Deciders:** Architecture Team
- **Tags:** data, lakehouse, storage, delta-lake, iceberg

## Context

The Lakehouse pattern stores data on object storage (S3/GCS/Azure Blob) in an open table format that adds ACID transactions, schema evolution, and time-travel to raw Parquet files. Three formats compete for this role: Delta Lake, Apache Iceberg, and Apache Hudi. The choice determines what compute engines can read the data without copying, how schema evolution and partition evolution work, what catalog integration is required, and how easy migration is if requirements change.

This decision is expensive to reverse because all existing tables must be fully rewritten to migrate between formats, and downstream jobs must be retested. Making it explicitly prevents three different teams from independently choosing three different formats.

In 2026, all three formats have reached production maturity. The choice is genuinely non-obvious. The key differentiator is the primary compute engine: Spark-first workloads favour Delta Lake; multi-engine and cloud-agnostic workloads favour Iceberg.

## Decision

We will use **Delta Lake** as the default table format for new lakehouse projects where Apache Spark is the primary compute engine.

Teams building multi-cloud deployments, integrating with Snowflake or BigQuery as a query engine, or running primarily on Trino without Spark should use **Apache Iceberg** instead. This exception should be documented in the project's architecture notes.

Apache Hudi is not recommended for new projects; its upsert capabilities are now available in Delta Lake and Iceberg without Hudi's additional operational complexity.

## Alternatives considered

### Option A: Delta Lake (chosen)
- **Pros:** Strongest Apache Spark integration (co-created by Databricks; donated to the Linux Foundation in 2019). Mature OPTIMIZE and Z-ORDER commands for file compaction and clustering. MERGE INTO syntax for upserts is well-optimised. Robust time-travel via `VERSION AS OF` and `TIMESTAMP AS OF`. `delta-rs` library enables Python and Rust access without Spark. Databricks uses Delta Lake natively — teams on Databricks pay no integration tax.
- **Cons:** Historically more tightly coupled to Databricks than Iceberg (though open-source Delta Lake and Databricks Delta are now nearly identical). Multi-engine support (Trino, Flink native) lags Iceberg slightly — the Delta Lake connector for Trino is maintained by the community, not the Trino project.

### Option B: Apache Iceberg
- **Pros:** Designed from the start for multi-engine access — Spark, Trino, Flink, Presto, Snowflake, BigQuery, Dremio, and StarRocks all support Iceberg natively without third-party connectors. Hidden partitioning means partition strategy can be changed without rewriting data. Partition evolution is first-class. Project Nessie adds Git-like catalog branching (create branches of your data catalog for safe experimentation). Better support for very large tables (> billions of rows) due to more efficient metadata management.
- **Cons:** `MERGE INTO` upsert performance lags Delta Lake in Spark workloads. More catalog complexity (requires an Iceberg REST catalog, Hive Metastore, or Nessie — Glue support is available but less mature). Less Databricks-native — teams on Databricks must explicitly configure Iceberg support.

### Option C: Apache Hudi
- **Pros:** Pioneer of the upsert-on-lake pattern; Copy-on-Write and Merge-on-Read table types allow trade-offs between write amplification and read performance. Good CDC ingestion story.
- **Cons:** Significantly smaller community than Delta Lake or Iceberg in 2026. The primary use case (efficient upserts) is now well-served by Delta Lake and Iceberg. Operational complexity is higher than both alternatives. Not recommended for new projects.

## Consequences

**Positive**

- A single table format across projects — engineers can move between data teams without re-learning table semantics
- `OPTIMIZE` and Z-ORDER are well-understood and documented for our primary Spark-on-EMR workload
- `delta-rs` enables lightweight Python access to Delta tables from notebooks without a Spark cluster
- Time-travel is available for auditing data states at any point in retention window

**Negative / trade-offs**

- Trino queries against Delta Lake use a community-maintained connector; Iceberg would give a first-class Trino integration
- Teams choosing Iceberg for valid multi-engine reasons must document the decision and cannot assume shared tooling
- Migration from Delta Lake to Iceberg requires a full table rewrite — the cost of getting this wrong is high

**Follow-up actions**

- [ ] Document the Iceberg exception criteria in the team onboarding guide
- [ ] Evaluate Delta Lake's Iceberg compatibility layer (`UniForm`) as a potential hedge that allows both formats to coexist on the same table
- [ ] Set VACUUM retention policy: minimum 30 days to support GDPR audit requirements; document this in the data retention policy
- [ ] Add a `delta-rs` notebook example to the internal tooling repo for analysts who need to read Delta tables without spinning up Spark
