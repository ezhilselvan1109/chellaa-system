# Alerting Strategy (Prometheus Alertmanager)

Alerting thresholds, severity classifications, and notification routes.

---

## 1. Alert Rules & Severities

| Alert Name | Condition | Severity | Notification Channel |
| :--- | :--- | :--- | :--- |
| `HighHttp5xxErrorRate` | HTTP 5xx errors $> 1\%$ for 3 minutes | Critical (P1) | PagerDuty / On-Call Phone |
| `HighCheckoutLatency` | p99 checkout response $> 500\text{ms}$ for 5 minutes | Warning (P2) | Slack #alerts-eng |
| `PostgresPoolSaturation`| Active connections $> 90\%$ for 2 minutes | Critical (P1) | PagerDuty / On-Call Phone |
| `KafkaConsumerLagHigh` | Consumer lag $> 5,000$ records for 10 minutes | Warning (P2) | Slack #alerts-eng |
| `PaymentGatewayDeclinesSpike`| Gateway decline rate $> 15\%$ for 5 minutes | High (P2) | Slack #alerts-finance |
| `MessagesInDLQ` | New messages detected in any `*.dlq` topic | Warning (P3) | Slack #alerts-eng |
