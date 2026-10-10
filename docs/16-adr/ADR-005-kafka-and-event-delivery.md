# ADR-005: Apache Kafka for Asynchronous Event Streaming and Transactional Outbox

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, Distributed Systems Engineer

---

## 1. Context

Cross-service workflows (search index synchronization, notifications, read-model aggregation, downstream fulfillment triggers) require asynchronous event delivery. We evaluated RabbitMQ, Apache Kafka, and AWS SQS/SNS.

---

## 2. Options Considered

1. **Option A: RabbitMQ**
   - *Pros*: Excellent complex routing; simple broker setup.
   - *Cons*: Transient message broker; lacks persistent distributed commit log; messages deleted after consumption (cannot replay past events to rebuild read models).
2. **Option B: Apache Kafka 3.7+ (KRaft Mode) (Chosen)**
   - *Pros*: Immutable distributed log; high throughput (>100k msgs/sec); configurable retention (14 days) enabling full event replay and CQRS rebuilds; strict partition ordering per aggregate root ID.
   - *Cons*: Operational complexity of Kafka clusters (mitigated by KRaft mode eliminating Zookeeper).

---

## 3. Decision

Adopt **Apache Kafka 3.7+ in KRaft mode** with `replication.factor=3`, `min.insync.replicas=2`, paired with the **Transactional Outbox Pattern** to prevent dual-write inconsistencies.

---

## 4. Consequences

- **Positive**: Guaranteed at-least-once delivery; full replayability for analytics and search indexing; zero message loss during database crashes.
- **Negative**: Consumers must implement local idempotency tables to handle duplicate message redelivery.

---

## 5. Related Documents
- [Kafka Architecture](file:///d:/learning/Microservice/backend/docs/07-events/kafka-architecture.md)
- [Outbox Pattern](file:///d:/learning/Microservice/backend/docs/04-architecture/outbox-pattern.md)
