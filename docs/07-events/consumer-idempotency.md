# Consumer Idempotency Architecture

Design patterns and database schema for bulletproof consumer deduplication.

---

## 1. At-Least-Once Delivery Reality

Because Kafka guarantees at-least-once delivery (a consumer can process a message and crash before acknowledging its offset), consumers will inevitably receive duplicate messages under real-world network partitions and pod restarts.

---

## 2. Idempotent Consumer Pattern

Every consumer method wraps its side effects within an atomic database transaction incorporating an insert into `consumer_idempotency_log`:

```sql
CREATE TABLE consumer_idempotency_log (
    event_id UUID NOT NULL,
    consumer_group VARCHAR(128) NOT NULL,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (event_id, consumer_group)
);
```

```java
@KafkaListener(topics = "commerce.orders.order-confirmed.v1", groupId = "shipping-service-order-consumer")
@Transactional
public void onOrderConfirmed(ConsumerRecord<String, OrderConfirmedEvent> record) {
    OrderConfirmedEvent event = record.value();
    
    // Check and record event processing atomically
    int inserted = entityManager.createNativeQuery(
        "INSERT INTO consumer_idempotency_log (event_id, consumer_group) " +
        "VALUES (:id, :grp) ON CONFLICT DO NOTHING")
        .setParameter("id", event.id())
        .setParameter("grp", "shipping-service-order-consumer")
        .executeUpdate();

    if (inserted == 0) {
        log.warn("Duplicate event {} ignored for group shipping-service-order-consumer", event.id());
        return;
    }

    // Execute stateful business logic (e.g. create shipment record)
    shipmentService.initiateShipmentForOrder(event.orderId(), event.items());
}
```
