# Distributed Transactions

Analysis of distributed transactional strategies, multi-phase commits (2PC) avoidance, and failure scenarios.

---

## 1. Why Two-Phase Commit (2PC) is Prohibited

Two-Phase Commit (2PC / XA transactions) across microservices introduces:
1. Tight blocking coupling across network boundaries.
2. Catastrophic latency inflation during network partitions.
3. Single point of failure if transaction coordinator crashes during the prepare phase.

**Architectural Mandate**: 2PC is strictly forbidden. All distributed transactions must use the **Orchestrated Saga Pattern** paired with the **Transactional Outbox Pattern**.

---

## 2. Distributed Failure Scenarios & Recovery Matrix

| Scenario | Root Cause | System Response & Recovery Action | User-Visible Status |
| :--- | :--- | :--- | :--- |
| **1. Payment Succeeds, Order Service Down** | Network partition after payment webhook | Payment Service records payment in DB. Webhook is retried or background reconciliation worker polls gateway and republishes `PaymentCompleted`. Order Service processes event upon recovery. | "Payment Received - Order Processing" |
| **2. Inventory Reserved, Payment Declined** | Insufficient customer funds | Order Service Saga triggers compensating command `ReleaseInventoryReservation` to Inventory Service. Stock returned to available pool. | "Payment Declined - Order Cancelled" |
| **3. Kafka Event Published More Than Once** | Network timeout during Kafka broker ACK | Consumer checks `consumer_idempotency_log` table by `eventId`. If already processed, skips domain action and acknowledges offset. | Seamless (No duplicate side-effects) |
| **4. Payment Webhook Arrives Multiple Times**| Gateway retry policy | Payment Service executes `INSERT INTO payment_transactions ON CONFLICT DO NOTHING`. Subsequent deliveries return HTTP 200 without duplicate crediting. | Seamless |
| **5. DB Commit Succeeds, Kafka Broker Down** | Infrastructure crash during event publish | Transactional Outbox pattern guarantees event is safely stored in RDBMS `outbox_messages` table. Outbox relay re-attempts publication once Kafka recovers. | Zero message loss |
| **6. Consumer Crashes Mid-Batch** | JVM Out-Of-Memory or pod restart | Kafka uncommitted offset is re-assigned to healthy consumer pod. Consumer uses idempotency table to safely skip already executed items. | Self-healing recovery |
| **7. Campaign Usage Limit Concurrency Race** | Concurrent requests for last available coupon | Promotion Service decrements usage counter using atomic database constraint (`UPDATE coupons SET used = used + 1 WHERE code = :code AND used < max`). Only the winning thread succeeds. | Winner gets discount; loser sees "Coupon Limit Reached" |
| **8. Search Indexing Falls Behind Catalog** | OpenSearch node reindexing | Catalog PostgreSQL remains authoritative source of truth. PDPs read directly from Catalog/Pricing DB; product search falls back to cached results until sync completes. | Normal shopping continues |
| **9. Notification Provider API Down** | Third-party provider outage (SendGrid/Twilio)| Notification Service buffers messages in local queue, retrying with exponential backoff (1m, 5m, 15m) before routing to DLQ. | Customer receives SMS/Email upon provider recovery |
| **10. Refund Times Out at Gateway** | Gateway network drop during refund POST | Refund Service marks transaction `PENDING_RECONCILIATION`. Automated reconciliation job polls gateway using original `refundId` to confirm or retry. | "Refund Processing" |
