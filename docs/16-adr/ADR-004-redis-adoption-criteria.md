# ADR-004: Redis Adoption Criteria and In-Memory Caching Strategy

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Performance Engineer

---

## 1. Context

High-throughput shopping traffic (catalog browsing, session carts, distributed rate limiting, and idempotency locks) requires sub-millisecond response times. We evaluated whether to use in-memory JVM caches, PostgreSQL tables, or Redis.

---

## 2. Options Considered

1. **Option A: In-Memory JVM Cache Only (Caffeine)**
   - *Pros*: Zero network hop latency (<1ms).
   - *Cons*: Cannot be shared across horizontal pod replicas; cannot enforce global API rate limits or distributed locks; cannot store guest carts across pod restarts.
2. **Option B: Distributed Redis Cluster (Chosen)**
   - *Pros*: Sub-millisecond distributed key-value store; atomic operations (`INCR`, `SET NX`); sliding window TTLs; built-in pub/sub.
   - *Cons*: Memory costs; requires strict volatile-lru eviction configuration to prevent out-of-memory errors.

---

## 3. Decision

Adopt **Redis 7.2** for:
- Guest carts (7-day sliding TTL).
- Distributed rate limiting (Token Bucket via Redis).
- Idempotency locks (60-second TTL).
- Product Detail Page and Category tree response cache (15-minute TTL).
Redis must NEVER be used as the permanent store of truth for durable business records.

---

## 4. Consequences

- **Positive**: Sub-50ms catalog read latency; rock-solid protection against duplicate checkout requests.
- **Negative**: Adds Redis cluster to infrastructure.

---

## 5. Related Documents
- [Redis Design](file:///d:/learning/Microservice/backend/docs/05-database/redis-design.md)
- [Caching Strategy](file:///d:/learning/Microservice/backend/docs/04-architecture/caching-strategy.md)
