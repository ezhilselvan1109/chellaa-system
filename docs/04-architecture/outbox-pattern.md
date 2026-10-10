# Transactional Outbox Pattern

Specification for dual-write prevention, atomic database-to-Kafka publishing, and outbox polling relays.

---

## 1. Problem Statement: Dual-Write Risk

Updating a relational database and publishing an event to Apache Kafka within the same application method creates a dual-write vulnerability:
- If the database commits and Kafka network call times out: Event is lost, downstream services never know.
- If Kafka publishes and the database transaction rolls back: Ghost event emitted, causing invalid state progression.

## 2. Architectural Solution: Transactional Outbox

```text
[Spring Service Method]
       |
       | (Local Database ACID Transaction)
       v
+--------------------------------------------------------+
| 1. INSERT INTO orders (...)                            |
| 2. INSERT INTO outbox_messages (id, topic, payload...) |
+--------------------------------------------------------+
       | (Commit)
       v
[PostgreSQL Database]
       |
       | (Asynchronous Polling / Debezium CDC)
       v
[Outbox Relay Worker]
       |
       v (acks=all)
[Apache Kafka Cluster]
       |
       v (Mark outbox row PROCESSED)
[PostgreSQL Database]
```

---

## 3. Outbox Table Schema

```sql
CREATE TABLE outbox_messages (
    id UUID PRIMARY KEY,
    aggregate_type VARCHAR(100) NOT NULL,
    aggregate_id VARCHAR(100) NOT NULL,
    topic VARCHAR(200) NOT NULL,
    partition_key VARCHAR(100) NOT NULL,
    payload JSONB NOT NULL,
    headers JSONB NOT NULL,
    status VARCHAR(30) NOT NULL DEFAULT 'PENDING', -- PENDING, PUBLISHED, FAILED
    retry_count INT NOT NULL DEFAULT 0,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    processed_at TIMESTAMPTZ
);

CREATE INDEX idx_outbox_pending ON outbox_messages (created_at) WHERE status = 'PENDING';
```
