# Observability Architecture

OpenTelemetry standards, unified metrics, distributed tracing, and structured logging.

---

## 1. Unified Observability Model (M-E-L-T)

```text
+---------------------------------------------------------------+
|                      Application Services                     |
|           (Spring Boot 3.3 Microservices with Micrometer)     |
+---------------------------------------------------------------+
     | (Logs - JSON)         | (Traces - W3C)       | (Metrics - Prometheus)
     v                       v                      v
+---------------------------------------------------------------+
|                   OpenTelemetry Collector                     |
|           (Processes, Batches, Redacts, and Routes)           |
+---------------------------------------------------------------+
     |                       |                      |
     v                       v                      v
[Elasticsearch / Loki]    [Jaeger / Tempo]      [Prometheus]
 (Central Log Search)    (Distributed Tracing)   (Time-Series Metrics)
     \                       |                      /
      \                      |                     /
       v                     v                    v
      +--------------------------------------------+
      |             Grafana Dashboards             |
      +--------------------------------------------+
```
