# Distributed Tracing Specification

W3C trace context, OpenTelemetry SDK instrumentation, and Jaeger span hierarchy.

---

## 1. Trace Propagation Topology

```text
[API Gateway] 
   | Injects: traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01
   v
[Order Service] (Span: POST /api/v1/orders/checkout)
   |
   +---> [Inventory Service] (Span: POST /api/v1/inventory/reserve)
   |
   +---> [PostgreSQL Write] (Span: INSERT orders)
   |
   +---> [Kafka Outbox Publish] (Span: Kafka.send commerce.orders.order-created.v1)
            |
            v
         [Payment Service] (Span: KafkaListener onOrderCreated)
```
Every step in the distributed transaction shares the exact same `traceId`, visible in Jaeger / Grafana Tempo.
