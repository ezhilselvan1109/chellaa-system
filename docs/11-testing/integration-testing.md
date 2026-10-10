# Integration Testing with Testcontainers

Integration tests against production-identical PostgreSQL, Redis, and Kafka Docker containers.

---

## 1. Testcontainers Architecture

To prevent false confidence from in-memory mocks (e.g. H2 database which behaves differently from PostgreSQL under concurrency):
- Spring Boot tests use `@Testcontainers` with dynamic properties:
  - `PostgreSQLContainer("postgres:16-alpine")`
  - `GenericContainer("redis:7.2-alpine")`
  - `KafkaContainer("confluentinc/cp-kafka:7.6.0")`
- Tests execute real Flyway migrations, real SQL constraints, real pessimistic row locks, and real Kafka event serialization.
