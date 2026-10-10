# Caching Strategy

Specification for multi-tier caching architectures, cache invalidation, and stampede prevention.

---

## 1. Caching Topology

```text
[Client / Browser] ----> Cache-Control: max-age=300 (Static Assets, Images)
          |
          v
[API Gateway] ---------> HTTP Response Cache for Category Hierarchies (TTL 10m)
          |
          v
[Microservice] --------> L1 In-Memory Cache (Caffeine - Hot metadata, TTL 1m)
          |
          v
[Distributed Cache] ---> L2 Redis Cluster (Catalog PDP, Price Books, Cart Session)
          |
          v
[PostgreSQL Database] -> Authoritative Transactional Source of Truth
```

---

## 2. Invalidation & Stampede Controls

1. **Cache-Aside with Jittered TTL**:
   - Keys are assigned an expiration TTL with random jitter ($\pm 10\%$) to prevent simultaneous expiration of thousands of keys (cache avalanche).
   - Category Tree: TTL 1 hour $\pm 6$ minutes.
   - Product Detail Page (PDP): TTL 15 minutes $\pm 90$ seconds.
   - User Cart Cache: TTL 7 days (sliding window).
2. **Event-Driven Cache Eviction**:
   - When a merchandiser updates a product in `catalog-service`, a `ProductUpdated` Kafka event triggers immediate eviction of the Redis key: `DEL cache:product:{productId}`.
3. **Strict Non-Caching Invariant**:
   - Stock balances during checkout and active payment amounts MUST NEVER be served from cache; they must be queried directly from PostgreSQL.
