# Grafana Dashboards Specification

Dashboard layouts for engineering and business operations.

---

## 1. Core Dashboards

1. **System Health & Golden Signals Dashboard**:
   - Aggregate RPS, HTTP 5xx error rate percentage, p50/p95/p99 latency graphs per microservice.
   - JVM Heap usage, Garbage Collection pause times, CPU/Memory per container.
2. **Kafka Streaming & Consumer Lag Dashboard**:
   - Inbound message rates per topic, consumer offset lag per consumer group, DLQ message count charts.
3. **Executive Commercial Operations Dashboard**:
   - Real-time orders per minute, hourly Gross Merchandise Value (GMV), payment conversion rate percentage, low-stock SKU count.
