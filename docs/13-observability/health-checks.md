# Health Checks Specification

Spring Boot Actuator liveness and readiness probe implementations.

---

## 1. Probe Separation

1. **Liveness Probe (`/actuator/health/liveness`)**:
   - Asserts: JVM process is alive and internal event loop is responsive.
   - Rule: Never check database or network connections here (avoids cascading container restarts when a database momentarily hiccups).
2. **Readiness Probe (`/actuator/health/readiness`)**:
   - Asserts: Service is ready to accept HTTP traffic.
   - Checks:
     - PostgreSQL database connection pool (`HikariCP` ping).
     - Redis connection ping.
     - Kafka broker metadata connectivity.
   - If PostgreSQL goes down, readiness returns HTTP 503 `OUT_OF_SERVICE`, causing Kubernetes Ingress to gracefully stop routing traffic until the database recovers.
