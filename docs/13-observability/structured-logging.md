# Structured Logging Standards

JSON logging schema, log levels, correlation IDs, and PII masking.

---

## 1. JSON Log Format (Logstash Logback Encoder)

All microservices emit machine-readable JSON logs to standard output:
```json
{
  "@timestamp": "2026-10-10T14:30:05.123Z",
  "level": "INFO",
  "service": "order-service",
  "traceId": "4bf92f3577b34da6a3ce929d0e0e4736",
  "spanId": "00f067aa0ba902b7",
  "userId": "c1d2e3f4-a5b6-7c8d-9e0f-1a2b3c4d5e6f",
  "thread": "http-nio-8080-exec-1",
  "logger": "com.ecommerce.order.application.OrderService",
  "message": "Order ORD-20261010-004821 created successfully",
  "orderId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "orderTotal": 2016.00
}
```

---

## 2. Automated PII & Secret Redaction

Logback configuration automatically sanitizes and redacts credit card patterns (`\b(?:\d[ -]*?){13,16}\b` -> `[REDACTED_PAN]`) and password keys (`"password":\s*".*?"` -> `"password":"[REDACTED]"`).
