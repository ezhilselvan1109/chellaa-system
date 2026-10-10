# Kafka Deployment

Cluster configurations, persistence, and JVM heap tuning.

---

## 1. Topologies

- **Local Dev**: Single-broker Kafka KRaft container (`KAFKA_ENABLE_KRAFT: yes`, `KAFKA_CFG_PROCESS_ROLES: broker,controller`).
- **Production**: Strimzi Kafka Operator on Kubernetes or AWS MSK (Managed Streaming for Kafka):
  - 3 Dedicated Broker Nodes (m6g.xlarge, 16GB RAM).
  - 3 Dedicated Controller Nodes.
  - EBS gp3 volumes with 3,000 IOPS and 125 MB/s throughput per broker.
  - JVM Heap: `-Xms4g -Xmx4g` with G1GC garbage collector.
