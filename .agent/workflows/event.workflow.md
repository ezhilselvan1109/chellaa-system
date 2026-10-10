# Event Workflow

## 1. Purpose & Objective
Design, version, publish, and consume asynchronous domain events over Apache Kafka with guaranteed delivery semantics (Transactional Outbox), idempotency, and resilient dead-letter handling.

## 2. Prerequisites & Inputs
- Domain event triggers identified in DDD aggregate modeling.
- Producing and consuming service boundary definitions.
- Ordering and throughput requirements.

## 3. Workflow Sequence

```text
Domain Event Identification & Purpose
                  |
                  v
Producer & Consumer Boundary Analysis
                  |
                  v
Schema Design & Versioning (CloudEvents Envelope)
                  |
                  v
Topic Strategy & Partition Key Selection
                  |
                  v
Delivery Semantics (At-least-once + Outbox)
                  |
                  v
Transactional Outbox & Relay Implementation
                  |
                  v
Consumer Deduplication & Idempotent Processing
                  |
                  v
Retry Policy & Dead Letter Queue (DLQ) Setup
                  |
                  v
Consumer Replay & Recovery Playbook
                  |
                  v
Integration & Chaos Failure Testing
                  |
                  v
Event Catalog & Documentation Publishing
```

## 4. Responsible Skills
- `kafka-event-design`
- `domain-design`
- `springboot-development`
- `testing-engineering`

## 5. Completion Gate
- Event payload uses standardized JSON/Avro format conforming to CloudEvents metadata.
- Partition key guarantees total in-order processing for aggregate instances (e.g. `orderId`).
- Producer publishes via local ACID Transactional Outbox table, eliminating dual-write risks.
- Consumer implements atomic deduplication (`consumer_idempotency_log` table).
- Failed events route to DLQ topic after 3 exponential backoff retries.
- Contract published in `docs/07-events/`.
