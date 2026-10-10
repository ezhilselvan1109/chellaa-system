# Asynchronous Communication

Specifications for message streaming, event routing, and consumer topologies using Apache Kafka.

---

## 1. Apache Kafka Topology & Semantics

1. **Broker Architecture**:
   - Deployed with 3 brokers running Kafka KRaft mode (no Zookeeper dependency).
   - Topic default configuration: `replication.factor=3`, `min.insync.replicas=2`.
2. **Delivery Guarantee**:
   - **At-Least-Once Delivery**: Producers publish using `acks=all` with transactional outbox polling.
   - Consumers implement **Idempotent Message Processing** via atomic local deduplication tables, achieving effectively-once semantics.
3. **Partition Key Selection**:
   - Every event specifies an explicit partition key derived from the aggregate root identifier (e.g., `orderId`, `customerId`, `sku`).
   - This guarantees that all state transitions for a specific aggregate instance are processed in strict sequential order.

## 2. Topic Strategy & Dead Letter Queues (DLQ)

```text
[Producer Service] 
       | (Publishes event)
       v
+-----------------------------------+
| Topic: commerce.orders.placed.v1  |
+-----------------------------------+
       |
       v
[Consumer Service] ---> (Fails 3x with backoff)
       |
       v (Dead Letter Publish)
+---------------------------------------+
| Topic: commerce.orders.placed.v1.dlq  |
+---------------------------------------+
       |
       v
[Alert / Operations Inspection Dashboard]
```
