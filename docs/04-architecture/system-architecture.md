# System Architecture

This document specifies the system architecture, infrastructure zones, request flows, and technology selections for the e-commerce platform.

---

## 1. Multi-Tier System Topology

```text
================================ CLIENT LAYER ================================
     [Customer Web (SPA)]                      [Admin Web (SPA)]
     (React 18 / Vite / TS)                 (React 18 / Vite / TS)
              \                                      /
               \                                    /
================================ EDGE / INGRESS LAYER =========================
                \                                  /
                 v                                v
          +----------------------------------------------+
          |            API Gateway Cluster               |
          |       (Spring Cloud Gateway / Envoy)         |
          |  - SSL Termination                           |
          |  - Distributed Rate Limiting (Redis)         |
          |  - JWT Claims Extraction & Validation        |
          |  - Correlation ID Injection                  |
          +----------------------------------------------+
                                 |
=========================== MICROSERVICES DOMAIN TIER =========================
                                 |
   +-----------------------------+-----------------------------+
   |                             |                             |
   v                             v                             v
[Identity Service]        [Catalog Service]           [Order Service]
(Spring Boot 3.3)         (Spring Boot 3.3)           (Saga Orchestrator)
   |                             |                             |
   v                             v                             v
[(DB) identity_db]        [(DB) catalog_db]           [(DB) order_db]
(PostgreSQL 16)           (PostgreSQL 16)             (PostgreSQL 16)
   |                             |                             |
   +-----------------------------+-----------------------------+
                                 |
                         (Transactional Outbox)
                                 v
=========================== EVENT STREAMING BUS ==============================
          +----------------------------------------------+
          |             Apache Kafka Cluster             |
          |         (3 Brokers / KRaft Mode)             |
          +----------------------------------------------+
                                 |
         +-----------------------+-----------------------+
         |                       |                       |
         v                       v                       v
[Search Service]        [Notification Service]    [Reporting Service]
(OpenSearch 2.13)       (Multi-Channel Engine)    (CQRS Read-Model)
```

---

## 2. Ingress Traffic Routing & Network Zones

1. **Public DMZ Zone**:
   - Only the API Gateway is provisioned with public IP addresses and DNS records (`api.store.com`, `admin-api.store.com`).
   - All inbound traffic must terminate TLS 1.3 at the Gateway.
2. **Private VPC Service Mesh**:
   - Microservices operate within an isolated Kubernetes cluster or Docker bridge network without public ingress.
   - Inter-service communication uses private DNS (`identity-service:8080`, `catalog-service:8080`).
3. **Data Persistence Zone**:
   - PostgreSQL instances, Redis clusters, and Kafka brokers reside in dedicated private database subnets protected by strict firewall security groups.
