# Event Replay and Disaster Recovery

Procedures for resetting consumer offsets, backfilling materialized read models, and disaster recovery.

---

## 1. Replay Scenarios

1. **Bug in Read-Model / Reporting Consumer**: A consumer logic bug corrupted analytical counters in `reporting_db`.
2. **New Consumer Onboarding**: A newly deployed Search Service or Recommendation Service needs to ingest all historical products.

---

## 2. Offset Reset Procedure

To replay historical events from the 14-day retention buffer:
1. Stop the target consumer service pods.
2. Truncate or drop the corrupted consumer read-model tables and clear `consumer_idempotency_log` entries for that specific consumer group.
3. Reset consumer group offset via Kafka CLI:
   ```bash
   kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
     --group reporting-service-group \
     --reset-offsets --to-earliest \
     --topic commerce.orders.order-confirmed.v1 \
     --execute
   ```
4. Restart consumer pods; verify streaming re-ingestion in logs and metrics.
