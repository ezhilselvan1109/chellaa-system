---
name: springboot-development
description: >-
  Use this skill to implement Spring Boot 3.3+ microservices using Java 21, clean hexagonal/layered architecture,
  Spring Data JPA, Spring Security, Flyway migrations, and Testcontainers automated testing.
---

# Spring Boot Development Skill

## 1. Purpose
Provides production-grade implementation guidelines for Java 21 and Spring Boot 3.3+ microservices, enforcing idiomatic layered architecture, declarative validation, database migrations, and clean unit/integration test suites.

## 2. When to Use It
- Scaffolding or implementing backend microservices in `services/`.
- Implementing REST controllers, application services, domain models, and repositories.
- Configuring Spring Security, JWT filters, and role-based method security (`@PreAuthorize`).
- Writing Flyway database migration scripts.
- Implementing Kafka producers with Transactional Outbox and Kafka listeners with error handlers.

## 3. Required Inputs
- Approved API contracts (`docs/06-api/`) and Event schemas (`docs/07-events/`).
- Database schema and indexing specs (`docs/05-database/`).
- Service boundary and domain models (`docs/03-domain/`).

## 4. Procedure
1. **Package Structure**:
   ```text
   com.ecommerce.<servicename>/
   ├── adapter/
   │   ├── in/web/          (REST Controllers, DTOs, Mappers)
   │   ├── in/event/        (Kafka Listeners, Event DTOs)
   │   ├── out/persistence/ (JPA Entities, Spring Data Repositories)
   │   └── out/event/       (Outbox Publisher, Kafka Client)
   ├── application/
   │   ├── port/in/         (Use Cases, Commands, Queries)
   │   ├── port/out/        (Persistence Ports, Gateway Ports)
   │   └── service/         (Application Business Services)
   ├── domain/              (Entities, Value Objects, Domain Exceptions)
   └── config/              (Security, Kafka, OpenAPI, Jackson)
   ```
2. **Layering & Separation of Concerns**:
   - Web controllers validate incoming requests (`@Valid`) and delegate to application ports.
   - Domain models encapsulate state and invariants; avoid anemic setters.
   - Database entities never escape the persistence adapter layer to web controllers.
3. **Database Transactions**:
   - Use `@Transactional(isolation = Isolation.READ_COMMITTED)` at application service boundaries.
   - Never perform outbound network HTTP calls inside `@Transactional` blocks.
4. **Outbox Pattern Implementation**:
   - Write aggregate change and `OutboxEvent` row within the same Spring transaction.
   - Scheduled publisher or CDC worker reads unread outbox rows and sends to Kafka.
5. **Security Setup**:
   - Stateless JWT resource server verification (`BearerTokenAuthenticationFilter`).
   - Role checks via `@PreAuthorize("hasAuthority('ROLE_ADMIN')")`.
6. **Testing**:
   - Unit test domain logic with JUnit 5 and AssertJ.
   - Integration test with `@SpringBootTest` and Testcontainers (PostgreSQL, Kafka).

## 5. Rules & Constraints
- Never commit secrets or hardcoded passwords; use `@ConfigurationProperties` and env vars.
- Never use `@Data` from Lombok on JPA entities (leads to recursive `hashCode`/`equals` bugs); use `@Getter`, `@Setter` carefully.
- Every mutating endpoint must return explicit DTOs, never raw JPA entities.

## 6. Expected Outputs
- Compilable, clean Java 21 / Spring Boot code.
- Passing unit and Testcontainers integration tests.

## 7. Validation Checklist
- [ ] Does `./mvnw clean verify` complete with zero errors?
- [ ] Are all external API inputs validated with `@Valid`?
- [ ] Does Actuator expose `/actuator/health` and `/actuator/prometheus`?

## 8. Common Failure Modes
- `LazyInitializationException` caused by accessing lazy JPA relations outside transactions.
- Dual-write race conditions when modifying database and pushing to Kafka in separate steps.

## 9. Authoritative Links
- [Spring Boot Architecture](file:///d:/learning/Microservice/backend/docs/04-architecture/microservices-architecture.md)
- [Testing Strategy](file:///d:/learning/Microservice/backend/docs/11-testing/testing-strategy.md)
