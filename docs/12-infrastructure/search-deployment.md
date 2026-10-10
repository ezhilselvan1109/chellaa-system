# OpenSearch Deployment

Cluster configuration, memory limits, and shard allocation.

---

## 1. Topologies

- **Local Dev**: Single-node OpenSearch 2.13 container (`discovery.type=single-node`, `DISABLE_SECURITY_PLUGIN=true`). JVM Heap: `-Xms512m -Xmx512m`.
- **Production**: AWS OpenSearch Service or OpenSearch Operator on Kubernetes:
  - 3 Cluster Manager Nodes + 3 Data Nodes.
  - JVM Heap set to exactly 50% of node RAM (max 31GB to preserve Compressed Object Pointers).
  - Automated hourly snapshot backups stored in S3.
