# Microservices Architecture

This document defines the structural architecture, application layering, and deployment topology of individual microservices.

---

## 1. Hexagonal / Clean Architecture Pattern

Every Spring Boot microservice adheres to Hexagonal Architecture (Ports and Adapters), ensuring complete separation between business domain logic and technical infrastructure:

```text
               +------------------------------------------------+
               |              Infrastructure Adapters           |
               |                                                |
               |  [Inbound Web Controller]   [Kafka Consumer]   |
               |           \                        /           |
               |            v                      v            |
               |   +----------------------------------------+   |
               |   |            Application Layer           |   |
               |   |        (Use Cases, Ports In/Out)       |   |
               |   |                   |                    |   |
               |   |                   v                    |   |
               |   |       +------------------------+       |   |
               |   |       |      Domain Core       |       |   |
               |   |       | (Entities, Invariants) |       |   |
               |   |       +------------------------+       |   |
               |   |                   ^                    |   |
               |   |                   |                    |   |
               |   +----------------------------------------+   |
               |            ^                      ^            |
               |           /                        \           |
               |  [JPA Persistence Adapter]  [Kafka Outbox Pub] |
               |                                                |
               +------------------------------------------------+
```

---

## 2. Layering Guidelines

1. **Domain Core (`domain`)**:
   - Zero framework dependencies (no Spring, no Hibernate, no Jackson).
   - Contains pure Java records, aggregates, value objects, and domain exceptions.
2. **Application Layer (`application`)**:
   - Implements use cases via incoming ports (`CreateOrderUseCase`, `CalculatePriceUseCase`).
   - Declares outbound ports (`OrderRepositoryPort`, `PaymentGatewayPort`).
   - Manages transaction boundaries (`@Transactional`).
3. **Adapters Layer (`adapter`)**:
   - **Inbound Web**: Spring MVC `@RestController`, request validation (`@Valid`), OpenAPI annotations.
   - **Inbound Event**: `@KafkaListener` consumers with deduplication handlers.
   - **Outbound Persistence**: Spring Data JPA repositories, entity mappers, Flyway migrations.
   - **Outbound Event**: Transactional Outbox writers and Kafka event publishers.
