---
name: kafka-event-design
description: >-
  Use this skill to design Apache Kafka event contracts, topic partition strategies, CloudEvents schemas,
  Transactional Outbox implementations, consumer idempotency, and Dead Letter Queue (DLQ) retry topologies.
---

# Kafka and Event Design Skill

## 1. Purpose
Designs high-throughput, fault-tolerant asynchronous event-driven architectures over Apache Kafka, ensuring strict event ordering, zero message loss via Transactional Outbox, and bulletproof consumer idempotency.

## 2. When to Use It
- Defining domain event schemas for cross-service publishing.
- Configuring Kafka topics, replication factors, and partition counts.
- Designing partition keys to guarantee strict ordering for aggregate instances.
- Setting up consumer group retry strategies and Dead Letter Queues (DLQ).
- Designing outbox tables and event deduplication tables.

## 3. Required Inputs
- Domain events from `docs/03-domain/domain-events.md`.
- Producer and consumer service definitions.
- Throughput and latency SLAs.

## 4. Procedure
1. **Define Event Contract**:
   - Conform to CloudEvents specification: `id` (UUID), `source`, `specversion` ("1.0"), `type` (e.g. `ecommerce.order.placed.v1`), `time` (ISO-8601 UTC), `datacontenttype` ("application/json"), `data` (domain payload).
2. **Topic Naming Convention**:
   - Format: `<domain>.<entity>.<event-name>.v<version>` (e.g., `commerce.orders.order-placed.v1`).
3. **Partition Key Selection**:
   - Choose the aggregate root ID (e.g., `orderId`, `customerId`, `productId`) as the Kafka message key to guarantee total order per aggregate.
4. **Publishing Strategy**:
   - Implement the **Transactional Outbox Pattern**: Store the event in the service's `outbox_messages` table within the same ACID transaction as the domain state change.
   - An Outbox Poller / Debezium relay publishes to Kafka with `acks=all`.
5. **Consumer Strategy**:
   - Consumer records processed `eventId` into an `idempotent_event_log` table within its local database transaction.
   - Ignore duplicates silently or acknowledge immediately.
6. **Error & DLQ Strategy**:
   - Transient failures: 3 retries with exponential backoff (e.g., 1s, 2s, 4s).
   - Poison pills / permanent errors: Route to `<topic>.dlq` with failure headers (`x-exception-message`, `x-failed-at`).
7. **Publish Event Contract**: Update `docs/07-events/`.

## 5. Rules & Constraints
- Never publish unversioned JSON payloads.
- Never write directly to Kafka in the middle of a database transaction (dual-write anti-pattern).
- Topic partition count cannot be reduced once provisioned.

## 6. Expected Outputs
- JSON Schema contracts for events in `docs/07-events/`.
- Producer/Consumer matrix table.

## 7. Validation Checklist
- [ ] Is the partition key explicitly defined to ensure ordered delivery?
- [ ] Is an idempotent consumer table schema specified?
- [ ] Is a Dead Letter Queue (DLQ) topic and alert configured for every consumer?

## 8. Common Failure Modes
- Using a random UUID as message key, causing events for the same order to arrive out of order across partitions.
- Consumer crashing after DB write but before Kafka commit, causing duplicate execution without deduplication.

## 9. Authoritative Links
- [Kafka Architecture](file:///d:/learning/Microservice/backend/docs/07-events/kafka-architecture.md)
- [Event Contract Standards](file:///d:/learning/Microservice/backend/docs/07-events/event-contract-standards.md)
- [Consumer Idempotency](file:///d:/learning/Microservice/backend/docs/07-events/consumer-idempotency.md)
