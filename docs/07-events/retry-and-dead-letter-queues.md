# Retry and Dead Letter Queue (DLQ) Strategy

Failure classification, consumer retry backoffs, and dead-letter routing.

---

## 1. Failure Classification

1. **Transient Errors**: Network timeouts, database connection drops, lock contention.
   - Strategy: Automatic retry with exponential backoff with jitter.
2. **Permanent Errors (Poison Pills)**: Malformed JSON, validation failure, unknown enum value, non-existent entity reference.
   - Strategy: Skip retries and route directly to Dead Letter Queue (DLQ) topic.

---

## 2. Spring Kafka DLQ Configuration

Using Spring Kafka `DefaultErrorHandler`:
- Backoff policy: 3 attempts with exponential intervals: 1s, 2s, 4s.
- `DeadLetterPublishingRecoverer`: On exhaustion, forwards the failed record to `<topic>.dlq` with added record headers:
  - `x-original-topic`: Source topic.
  - `x-exception-message`: Exception message string.
  - `x-exception-stacktrace`: Truncated stack trace.
  - `x-failed-timestamp`: UTC timestamp of terminal failure.
- Alerts: A Prometheus metric `kafka_dlq_messages_total` triggers an alert when messages arrive in any `.dlq` topic.
