# Database Technology Selection

Evaluation of data store technologies against workload characteristics, transaction guarantees, and operational trade-offs.

---

## 1. Technology Selection Matrix

| Technology | Selected Role | Trade-offs Evaluated | Justification & Workload Fit |
| :--- | :--- | :--- | :--- |
| **PostgreSQL 16** | **Primary Relational Store** | Operational maturity vs. horizontal sharding complexity. | Default transactional source of truth. Unrivaled ACID compliance, rich indexing (B-Tree, GIN, Partial), strong JSONB support, and mature ecosystem. |
| **MongoDB 7.0** | **Optional / Document Store** | High developer velocity vs. lack of cross-document relational constraints. | Strictly restricted to polymorphic schemas where schema-less flexibility is justified (e.g., dynamic notification template rendering logs and audit payloads). |
| **Redis 7.2** | **In-Memory Cache & Key-Value** | In-memory speed vs. RAM cost and durability risk. | Sub-millisecond latency for ephemeral state: guest carts, distributed locks, rate limiting tokens, and session caches. Not for durable business records. |
| **OpenSearch 2.13** | **Search & Analytics Engine** | Distributed cluster overhead vs. inverted index query speed. | Full-text catalog search, fuzzy matching, faceted filtering, and autocomplete. Strictly a derived index populated from PostgreSQL via Kafka. |
| **MinIO / AWS S3** | **Object Storage** | Object retrieval latency vs. massive scale and low cost. | Binary asset storage: product images, videos, downloadable PDF invoices, and customer return inspection media. |
