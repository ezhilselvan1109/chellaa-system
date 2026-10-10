---
name: observability-engineering
description: >-
  Use this skill to configure OpenTelemetry distributed tracing, structured JSON logging, Prometheus metrics,
  Spring Boot Actuator health checks, Grafana dashboards, and Service Level Objectives (SLOs).
---

# Observability Engineering Skill

## 1. Purpose
Provides end-to-end visibility across distributed microservices through unified structured logging, correlation IDs, OpenTelemetry distributed tracing, RED/USE metrics, and actionable health checks.

## 2. When to Use It
- Instrumenting Spring Boot microservices with OpenTelemetry, Micrometer, and Logback.
- Ensuring correlation ID and trace propagation across HTTP calls and Kafka event headers.
- Configuring Spring Boot Actuator readiness (`/actuator/health/readiness`) and liveness (`/actuator/health/liveness`) probes.
- Setting up SLIs/SLOs and alerting thresholds (e.g. error rate > 1%, p99 latency > 500ms).
- Diagnosing distributed transaction latency spikes or consumer lag.

## 3. Required Inputs
- Service topology and critical customer journeys.
- Target SLOs and alerting policies (`docs/13-observability/slo-and-sli.md`).
- Established observability architecture (`docs/13-observability/`).

## 4. Procedure
1. **Configure Structured Logging**:
   - Output logs in JSON format via Logstash Logback Encoder.
   - Include standard fields: `@timestamp`, `level`, `service`, `traceId`, `spanId`, `userId`, `thread`, `message`, `stackTrace`.
   - Redact sensitive data (passwords, card CVVs, PII tokens).
2. **Implement Trace Propagation**:
   - Extract and inject W3C `traceparent` headers across HTTP requests via OpenTelemetry SDK.
   - Inject `traceId` and `spanId` into Kafka record headers (`ce-traceparent`).
3. **Metrics Collection (RED Method)**:
   - Rate (Requests per second via `http.server.requests`).
   - Errors (HTTP 5xx count / failed Kafka messages).
   - Duration (Timer histograms with percentiles: p50, p95, p99).
4. **Health Check Probes**:
   - Liveness probe: Reports `UP` if process is healthy and event loop is not blocked.
   - Readiness probe: Verifies active connections to PostgreSQL, Redis, and Kafka before accepting traffic.
5. **Publish Observability Specs**: Update `docs/13-observability/`.

## 5. Rules & Constraints
- NEVER log customer credentials, auth tokens, credit card numbers, or secret keys.
- Do not make external database or network calls inside liveness health checks (avoids false restarts).
- Log messages must use structured key-value MDC context, not string concatenation.

## 6. Expected Outputs
- OpenTelemetry and Logback configuration files.
- Grafana dashboard definitions and Prometheus alert rules in `docs/13-observability/`.

## 7. Validation Checklist
- [ ] Does a request entering the API Gateway carry the same `traceId` across downstream services and Kafka consumers?
- [ ] Are PII and payment tokens scrubbed from logs?
- [ ] Do readiness probes correctly turn `DOWN` if PostgreSQL becomes unreachable?

## 8. Common Failure Modes
- High-cardinality metrics (e.g. putting `userId` or `orderId` in Prometheus metric tags), crashing Prometheus memory.
- Logging full request bodies containing credit card numbers.

## 9. Authoritative Links
- [Observability Architecture](file:///d:/learning/Microservice/backend/docs/13-observability/observability-architecture.md)
- [Distributed Tracing](file:///d:/learning/Microservice/backend/docs/13-observability/distributed-tracing.md)
- [SLO and SLI](file:///d:/learning/Microservice/backend/docs/13-observability/slo-and-sli.md)
