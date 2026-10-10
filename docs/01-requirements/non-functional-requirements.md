# Non-Functional Requirements

This document defines the quantitative quality attributes, service level objectives (SLOs), and architectural constraints for the single-owner e-commerce platform.

---

## 1. Performance & Latency Targets

- **NFR-PERF-01 (Catalog Read Latency)**: Public product search and product detail page (PDP) queries shall achieve p95 response times under 50ms and p99 response times under 120ms under baseline loads.
- **NFR-PERF-02 (Cart & Checkout Latency)**: Cart mutation and checkout price computation endpoints shall achieve p95 response times under 100ms and p99 response times under 200ms.
- **NFR-PERF-03 (Throughput Capacity)**: The system shall sustain a minimum peak load of 5,000 requests per second (RPS) on read paths and 500 RPS on write paths (checkout/payment) during promotional flash sales without degradation.
- **NFR-PERF-04 (Search Sync Freshness)**: Catalog updates committed in PostgreSQL shall be reflected in OpenSearch within 2.0 seconds under normal load.

---

## 2. Scalability & Concurrency

- **NFR-SCAL-01 (Horizontal Microservice Scaling)**: All microservices shall be stateless at the application layer, permitting horizontal auto-scaling from 2 to 20 pod replicas based on CPU/memory utilization (>70% threshold).
- **NFR-SCAL-02 (Zero Inventory Overselling)**: Under high concurrent checkout attempts for a single limited-stock SKU (e.g., 50 concurrent requests competing for 5 available units), the system shall strictly allocate exactly 5 units and reject or waitlist the remaining 45 requests with zero inventory over-allocation.
- **NFR-SCAL-03 (Database Connection Efficiency)**: Each microservice instance shall pool database connections via HikariCP, limiting maximum active pool size to 20 connections per pod to prevent PostgreSQL connection saturation.

---

## 3. Reliability, Availability & Fault Tolerance

- **NFR-REL-01 (System Availability)**: The platform shall target 99.95% monthly uptime (<21.9 minutes of unplanned downtime per month) for public shopping paths.
- **NFR-REL-02 (Graceful Degradation)**: If non-critical services (e.g., Review Service or Recommendation Service) become unavailable, core shopping and checkout paths must continue functioning without user-visible disruption.
- **NFR-REL-03 (Event Durability & Replay)**: Apache Kafka topics shall retain domain events for a minimum of 14 days, enabling replay and reconstruction of read models and reporting stores in the event of consumer corruption.
- **NFR-REL-04 (Saga Recovery & Compensation)**: If a distributed checkout saga step fails (e.g. payment decline or network timeout), compensating actions shall execute within 5 seconds to release held inventory and restore clean system state.

---

## 4. Security & Compliance

- **NFR-SEC-01 (Transport Security)**: All external and internal traffic shall be encrypted in transit using TLS 1.3. Plain HTTP traffic must be rejected or redirected to HTTPS at the API Gateway.
- **NFR-SEC-02 (Zero Raw Card Storage)**: The platform shall maintain PCI-DSS compliance boundaries by storing zero cardholder data (PAN, CVV, PIN). All card interactions shall occur via tokenized iframe/hosted fields provided by accredited gateways.
- **NFR-SEC-03 (Data at Rest Encryption)**: Customer personal identifiable information (PII) including email, phone numbers, and physical delivery addresses shall be encrypted at rest in PostgreSQL using AES-256.
- **NFR-SEC-04 (Defense Against OWASP Top 10)**: All input payloads must be strictly sanitized; all database queries must use parameterized SQL via JPA to prevent SQL injection; API Gateway shall enforce rate-limiting of 60 requests/minute per IP on authentication endpoints.

---

## 5. Observability & Maintainability

- **NFR-OBS-01 (Distributed Tracing)**: Every HTTP request and Kafka event shall propagate W3C standard trace context headers (`traceparent`), enabling 100% end-to-end distributed trace reconstruction across gateway, microservices, and background consumers.
- **NFR-OBS-02 (Structured JSON Logging)**: 100% of service logs shall be emitted in machine-readable JSON format, containing `timestamp`, `service`, `traceId`, `spanId`, `level`, and structured metadata, with zero plaintext secrets or PII.
- **NFR-OBS-03 (Health Probes)**: Every microservice shall expose Kubernetes-compliant liveness (`/actuator/health/liveness`) and readiness (`/actuator/health/readiness`) endpoints responding within 200ms.
