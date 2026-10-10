# ADR-012: Containerized Deployment and OpenTelemetry Observability Stack

- **Status**: Approved
- **Date**: 2026-10-10
- **Deciders**: Architecture Team, DevOps Lead

---

## 1. Context

Operating 16 distributed microservices across developer machines, staging clusters, and production requires consistent packaging, zero-drift environments, and standardized observability telemetry. We evaluated deployment toolchains and monitoring frameworks.

---

## 2. Options Considered

1. **Option A: Bare Metal / VM Deployment with Proprietary APM**
   - *Pros*: Simple single-machine hosting initially.
   - *Cons*: Severe environment drift between local dev and production; expensive proprietary vendor lock-in for APM telemetry.
2. **Option B: Multi-Stage Containers, Docker Compose / Kubernetes, and OpenTelemetry (Chosen)**
   - *Pros*: 100% reproducible local environments via Docker Compose; horizontal pod auto-scaling via Kubernetes in production; vendor-neutral OpenTelemetry standards for traces, metrics, and structured JSON logs.
   - *Cons*: Requires understanding container networking and Kubernetes Helm charts.

---

## 3. Decision

Adopt **Multi-Stage Docker containers**, **Docker Compose** for local dev, **Kubernetes / Helm** for production, and **OpenTelemetry (W3C traceparent)** with Prometheus, Grafana, and Jaeger for full-stack observability.

---

## 4. Consequences

- **Positive**: Seamless environment reproducibility, automated CI/CD verification, unified end-to-end distributed trace visibility.
- **Negative**: Developers must have Docker installed locally.

---

## 5. Related Documents
- [Observability Architecture](file:///d:/learning/Microservice/backend/docs/13-observability/observability-architecture.md)
- [Local Development](file:///d:/learning/Microservice/backend/docs/12-infrastructure/local-development.md)
