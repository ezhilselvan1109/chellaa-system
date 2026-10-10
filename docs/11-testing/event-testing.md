# Event Testing (Kafka Producers and Consumers)

Testing transactional outbox publications, consumer deduplication, and DLQ routing.

---

## 1. Producer Event Verification

- Uses `EmbeddedKafka` or Testcontainers Kafka.
- Asserts that when a business entity (e.g. `Order`) commits in PostgreSQL, an outbox row is inserted and subsequently published to the target Kafka topic matching CloudEvents JSON schema.

---

## 2. Consumer Failure & Idempotency Testing

- Publishes duplicate event records with identical `id` to the topic. Asserts that the consumer executes domain logic exactly once.
- Publishes poison-pill malformed JSON payloads. Asserts that after 3 backoff retries, the record is forwarded to the corresponding `.dlq` topic with exception headers.
