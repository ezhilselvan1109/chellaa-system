# Microservice Development Workflow

## 1. Purpose & Objective
Define the standardized lifecycle for creating, scaffolding, developing, and deploying an autonomous Spring Boot microservice within the enterprise e-commerce platform.

## 2. Prerequisites & Inputs
- Approved ADR justifying the service boundary.
- Documented domain model and aggregate definitions.
- Assigned data store and database isolation plan.

## 3. Workflow Sequence

```text
Service Responsibilities Definition
                |
                v
Bounded Context & Entity Invariants
                |
                v
Owned Data & Schema Isolation
                |
                v
Database Migrations (Flyway/Liquibase)
                |
                v
REST API Contracts (OpenAPI Spec)
                |
                v
Kafka Event Contracts (Producers & Consumers)
                |
                v
Security Configuration (Spring Security, JWT, mTLS)
                |
                v
Observability Setup (OpenTelemetry, Actuator, Health)
                |
                v
Application Layer Implementation
                |
                v
Automated Testing (Unit, Testcontainers Integration)
                |
                v
Failure Verification & Chaos Testing
                |
                v
Service Documentation & Contract Publishing
```

## 4. Responsible Skills
- `architecture-design`
- `domain-design`
- `database-design`
- `api-design`
- `kafka-event-design`
- `springboot-development`
- `security-engineering`
- `testing-engineering`
- `observability-engineering`

## 5. Completion Gate
- Independent build passes cleanly (`./mvnw clean verify`).
- Service starts up with passing health checks (`/actuator/health`).
- OpenAPI docs exposed at `/v3/api-docs`.
- Testcontainers integration tests pass against PostgreSQL/Kafka without mocks.
- Dockerfile builds and passes security scanning.
