# Redis Deployment

Redis Standalone vs. Redis Cluster topologies and memory policies.

---

## 1. Topologies

- **Local Dev**: Redis 7.2 Standalone container with AOF (Append-Only File) persistence enabled: `appendonly yes`.
- **Production**: Redis Cluster (3 Masters, 3 Replicas) with Redis Sentinel / AWS ElastiCache for multi-AZ failover.
- **Memory Max & Eviction**:
  - `maxmemory 4gb`
  - `maxmemory-policy volatile-lru` (Evicts keys with an explicit TTL using LRU algorithm when memory fills up, protecting persistent keys).
