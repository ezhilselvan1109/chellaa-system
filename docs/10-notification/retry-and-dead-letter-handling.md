# Retry and Dead Letter Handling in Notifications

Handling provider downtime, rate-limiting HTTP 429s, and unroutable addresses.

---

## 1. Retry Policies

1. **Transient Provider Outages**:
   - HTTP 429 (Rate Limit Exceeded) or HTTP 5xx (Provider Server Error).
   - Retried up to 3 times using exponential backoff with jitter: 1 minute, 5 minutes, 15 minutes.
2. **Terminal Delivery Failures**:
   - HTTP 400 (Invalid phone number / unparseable email address) or Meta Policy Error (Message outside 24h window).
   - Immediately marked `FAILED` without retries, routed to DLQ topic: `commerce.notifications.dispatch-failed.v1.dlq`.
