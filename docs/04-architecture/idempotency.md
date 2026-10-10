# Idempotency Architecture

This document defines idempotency protocols across REST API mutation requests and asynchronous Kafka event consumers.

---

## 1. REST API Idempotency via `Idempotency-Key`

Critical mutating endpoints (`POST /api/v1/orders/checkout`, `POST /api/v1/payments`, `POST /api/v1/refunds`) mandate an `Idempotency-Key: <UUID>` HTTP header.

```text
[Client] ---> POST /api/v1/orders/checkout [Idempotency-Key: X]
                 |
                 v
           [API Layer] ---> Check Redis Key: `idemp:{X}`
                               /             \
                  (Key Exists)                (Key Absent)
                             /                  \
             Return Cached Response             Acquire Redis Lock (TTL 60s)
                                                Execute Business Logic
                                                Store Result in Redis & Return
```

---

## 2. Kafka Consumer Idempotency

Every Kafka listener verifies message novelty within its local database transaction using a dedicated `consumer_idempotency_log` table:

```sql
CREATE TABLE consumer_idempotency_log (
    message_id UUID NOT NULL,
    consumer_group VARCHAR(100) NOT NULL,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (message_id, consumer_group)
);
```

```java
@Transactional
public void handleDomainEvent(DomainEvent event) {
    boolean inserted = idempotencyRepository.tryInsert(event.id(), CONSUMER_GROUP);
    if (!inserted) {
        log.info("Duplicate event {} already processed. Skipping.", event.id());
        return;
    }
    // Execute domain side effects...
}
```
