# ADR-0004: pgvector as the default vector store

- **Status:** Accepted
- **Date:** 2026-05-02
- **Deciders:** Architecture Team
- **Tags:** ai, rag, vector-store, database

## Context

The RAG pattern and any system that performs semantic similarity search needs a vector store. The choice determines where embeddings live, how retrieval queries are expressed, and how access control integrates with the rest of the data layer. The options range from adding an extension to an existing Postgres instance (pgvector) to adopting a dedicated vector database (Pinecone, Qdrant, Weaviate). This decision is expensive to reverse because embeddings must be re-ingested into a new store and all retrieval queries must be rewritten.

Teams starting a new RAG system in 2026 typically already operate Postgres for their application data. The question is whether the vector workload warrants a separate, specialised system from day one.

## Decision

We will use **PostgreSQL with the pgvector extension** as the default vector store.

Teams may adopt a dedicated vector database (Qdrant as the recommended alternative) when the vector corpus exceeds 5 million chunks, multi-tenancy namespace isolation is required at retrieval time, or filtering + similarity search combined query latency exceeds 200 ms on a right-sized Postgres instance.

## Alternatives considered

### Option A: PostgreSQL + pgvector (chosen)
- **Pros:** No new infrastructure — if you already run Postgres, you get a vector store for the cost of `CREATE EXTENSION vector`. Joins with application data are trivial (attach document metadata, ACL, tenant filters in the same query). Familiar operational model (backups, replicas, monitoring). Transactional consistency between document metadata and embeddings. Free.
- **Cons:** Not designed for vector-only workloads; at > 5M vectors, approximate nearest-neighbour (HNSW) index build time and memory footprint become significant. Does not support multi-tenancy namespaces natively — you partition with a `WHERE tenant_id = $1` clause, which is less efficient than a purpose-built namespace. Maximum vector dimensionality is 2,000 (sufficient for all current embedding models ≤ 1,536 dimensions).

### Option B: Qdrant
- **Pros:** Purpose-built for vector search: native HNSW, filtering, and payload indexing at scale. Built-in collection namespaces map cleanly to multi-tenant isolation. Cloud-hosted managed option or self-hosted via Docker/Kubernetes. Rust core — very fast and memory-efficient. Strong filtering performance (scalar quantisation, binary quantisation options).
- **Cons:** Adds a new managed service to the infrastructure stack. Joining with application database requires application-side logic or a separate metadata fetch. Operational team must learn a new system. Free tier is limited; managed Qdrant Cloud has per-node pricing.

### Option C: Pinecone
- **Pros:** Fully managed SaaS — no infrastructure to operate. Excellent performance and reliability SLA. Native multi-tenancy via namespaces. Good client library support.
- **Cons:** Vendor lock-in — data and queries are tied to Pinecone's proprietary API. Expensive at medium-to-large scale ($70+/month for a starter pod, scales steeply). No joins with application data. Data leaves your cloud account — requires DPA for GDPR; not viable for HIPAA-sensitive data without careful scoping.

### Option D: Weaviate
- **Pros:** Open-source, self-hostable, multi-modal (text + image vectors in the same store). GraphQL query API with filtering. Modular vectorizer plugins.
- **Cons:** More operationally complex than Qdrant or pgvector. GraphQL adds a query translation layer for teams used to SQL. Smaller community than Pinecone or pgvector. Cloud offering (Weaviate Cloud) is more expensive than Qdrant Cloud.

## Consequences

**Positive**

- No new infrastructure for teams already running Postgres — same backup, monitoring, and HA strategy
- ACL and multi-tenant filtering expressed as standard SQL `WHERE` clauses — no separate query language to learn
- Transactional consistency: document metadata and its embedding vector are in the same row; they cannot get out of sync
- Migration to a dedicated vector DB later is a well-understood operation (re-embed and re-ingest)

**Negative / trade-offs**

- Postgres is not optimised for approximate nearest-neighbour at scale; HNSW index build time on a 5M-vector corpus is several hours on typical hardware
- The `ivfflats` index type (faster build) sacrifices recall accuracy; HNSW is preferred but memory-heavy
- pgvector retrieval and application queries share the same Postgres connection pool — a slow vector query can affect application latency if not isolated to a read replica

**Follow-up actions**

- [ ] Create a read replica for all vector similarity queries; never run `<=>` queries against the primary
- [ ] Set `max_vectors_per_list` and HNSW `m` / `ef_construction` parameters based on corpus size benchmarks
- [ ] Define the migration trigger: schedule a review when any single collection exceeds 5M chunks
- [ ] Document the Qdrant migration runbook so it can be executed in < 1 week when the trigger is reached
