# ADR-006: OpenSearch 2.13 for Catalog Search and Autocomplete

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Search Engineer

---

## 1. Context

Customers require lightning-fast catalog discovery: full-text search across titles/descriptions, typo-tolerant fuzzy matching, prefix autocomplete, and multi-facet filtering (categories, brands, price ranges, ratings). We evaluated PostgreSQL Full-Text Search vs. OpenSearch.

---

## 2. Options Considered

1. **Option A: PostgreSQL Full-Text Search (tsvector / pg_trgm)**
   - *Pros*: Zero additional infrastructure; immediate consistency with catalog tables.
   - *Cons*: Limited relevance ranking algorithms; slow faceted aggregations across large catalogs under high concurrency; heavy search queries degrade transactional database CPU.
2. **Option B: OpenSearch 2.13 / Elasticsearch (Chosen)**
   - *Pros*: Specialized Lucene inverted index; sub-30ms autocomplete using edge-ngram tokenizers; powerful aggregations for faceted navigation; independent scaling from catalog database.
   - *Cons*: Derived index requires background synchronization via Kafka CDC events; eventual consistency (~2s delay).

---

## 3. Decision

Adopt **OpenSearch 2.13** as the dedicated search engine for `search-service`. Authoritative catalog data remains in PostgreSQL; OpenSearch is strictly a derived index.

---

## 4. Consequences

- **Positive**: Sub-30ms search queries, superior typo tolerance, offloads read traffic from transactional database.
- **Negative**: Catalog updates reflect in search with a brief eventual consistency window (<2s).

---

## 5. Related Documents
- [Search Index Design](file:///d:/learning/Microservice/backend/docs/05-database/search-index-design.md)
- [Catalog Service Architecture](file:///d:/learning/Microservice/backend/docs/04-architecture/system-architecture.md)
