# Scalability Strategy

Architecture for horizontal service scaling, database read replication, and flash-sale load handling.

---

## 1. Horizontal Auto-Scaling (HPA)

1. **Stateless Microservices**:
   - Application instances store zero local session state.
   - Kubernetes Horizontal Pod Autoscalers (HPA) scale pods between 2 and 20 replicas based on CPU target 70% or incoming request queue latency.
2. **Fast Startup Times**:
   - Spring Boot 3.3 utilizes Spring AOT or optimized JVM class-data sharing (CDS) ensuring pod readiness in under 8 seconds.

---

## 2. Database Scaling: Primary & Read Replicas

1. **PostgreSQL Read Replicas**:
   - High-throughput read services (Catalog, Pricing) route read-only queries to asynchronous PostgreSQL read replicas using Spring `AbstractRoutingDataSource`.
   - Write mutations route strictly to the Primary Writer node.
2. **Connection Pool Management (HikariCP)**:
   - Max 20 connections per pod.
   - PgBouncer deployed as connection multiplexer between Kubernetes pods and PostgreSQL nodes, supporting thousands of concurrent client connections without overwhelming database memory.
