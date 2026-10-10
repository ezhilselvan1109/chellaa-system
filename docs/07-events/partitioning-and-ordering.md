# Partitioning and Ordering Guarantees

Rules for message key hashing, strict ordering, and avoiding partition rebalance stalls.

---

## 1. Per-Aggregate Strict Ordering Guarantee

Kafka guarantees total ordering of records strictly **within a single partition**.
- By designating the aggregate root identifier (e.g. `orderId`, `customerId`) as the Kafka message key, Kafka's default murmur2 partitioner guarantees that all events for that specific aggregate route to the exact same partition.
- Result: An `OrderShipped` event is guaranteed never to be processed before an `OrderCreated` event for that order.

---

## 2. Partition Skew Prevention

- Keys must have high cardinality (e.g. UUIDs).
- Never use coarse categories or status strings as partition keys (e.g. using `status=PAID` would route 90% of traffic to a single partition, hot-spotting one broker).
