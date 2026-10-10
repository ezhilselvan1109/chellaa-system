# Configuration Management

Spring Boot profiles, environment variable overrides, and dynamic configs.

---

## 1. Spring Boot Profile Hierarchy

- `application.yml`: Baseline configurations shared across all environments (Jackson, OpenAPI, JPA dialects).
- `application-dev.yml`: Local Docker Compose URLs (`localhost:5432`, `localhost:9092`), mock payment providers enabled, debug logging.
- `application-test.yml`: Testcontainers dynamic properties, WireMock servers.
- `application-prod.yml`: Strict SSL verification, OpenTelemetry OTLP endpoint exporter, production connection pools.
- Environment variable overrides adhere to standard 12-Factor App rules: `SPRING_DATASOURCE_URL`, `SPRING_KAFKA_BOOTSTRAP_SERVERS`.
