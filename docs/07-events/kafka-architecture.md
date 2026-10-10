# Kafka Cluster Architecture

Cluster specifications, topic provisioning, replication, and broker configuration.

---

## 1. Broker Infrastructure (Apache Kafka 3.7+ KRaft)

- **Cluster Size**: 3 Brokers (minimum for quorums without Zookeeper).
- **Default Replication Factor**: 3 (all topic partitions replicated across all 3 nodes).
- **Minimum In-Sync Replicas (`min.insync.replicas`)**: 2 (ensures acknowledgments only occur when data is durable on at least two independent disks).
- **Producer Config**: `acks=all`, `enable.idempotence=true`, `max.in.flight.requests.per.connection=5`.
- **Consumer Config**: `enable.auto.commit=false` (manual offset commit strictly after database transaction commits).

---

## 2. Topic Partitioning & Sizing Strategy

- **Default Partitions per Topic**: 6 partitions.
- **Partition Throughput Budget**: ~1,000 messages/sec per partition. 6 partitions comfortably sustain 6,000 messages/sec, easily accommodating our 5,000 RPS peak targets.
