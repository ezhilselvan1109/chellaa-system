# Analytical Event Ingestion

Kafka event consumer topologies, deduplication, and late-arriving event handling.

---

## 1. Event Consumer Handlers

The `reporting-service` subscribes to:
- `commerce.orders.order-confirmed.v1`: Increments gross sales, order counters, and category revenue.
- `commerce.refunds.refund-completed.v1`: Records deductions from daily net revenue.
- `commerce.inventory.inventory-deducted.v1`: Updates SKU velocity counters.
- `commerce.promotion.campaign-redeemed.v1`: Updates promotional budget tracking.

---

## 2. Late-Arriving Event Handling

Events arriving out of order (e.g. refund arriving 10 days after order placement):
- Aggregations are bucketed by the event's business effective date (`event.time`), updating the specific historical date partition rather than attributing the event to the current timestamp.
