# Metrics Specification (Prometheus / Micrometer)

RED method (Rate, Errors, Duration) metrics, business counters, and JVM telemetry.

---

## 1. Golden Signals / RED Metrics

Exposed via `/actuator/prometheus` on port 8080:
- **Rate**: `http_server_requests_seconds_count{uri="/api/v1/orders/checkout"}`
- **Errors**: `http_server_requests_seconds_count{status=~"5.."}`
- **Duration**: `http_server_requests_seconds_max` and quantiles (p50, p95, p99).

---

## 2. Domain & Business Metrics

- `commerce_orders_created_total{status="PAID"}`: Total completed orders counter.
- `commerce_inventory_reservations_failed_total{reason="INSUFFICIENT_STOCK"}`: Stockout event tracker.
- `commerce_payments_declined_total{provider="RAZORPAY"}`: Payment failure tracker.
- `kafka_consumer_lag_records{group="order-service-group"}`: Real-time consumer offset lag.
