# Local Development Setup

Prerequisites, quick start runbook, and developer ergonomics.

---

## 1. Prerequisites

- **Java**: Eclipse Temurin JDK 21+
- **Node.js**: Node.js 20+ LTS & npm 10+
- **Docker**: Docker Engine 25+ with Docker Compose v2
- **Build Tool**: Maven Wrapper (`./mvnw`) or Gradle Wrapper

---

## 2. Quick Start: Spinning Up Infrastructure

1. Clone repository and start backing services via Docker Compose:
   ```bash
   cd infrastructure/docker
   docker compose up -d postgres redis kafka opensearch minio
   ```
2. Verify all containers are healthy:
   ```bash
   docker compose ps
   ```
3. Run any individual microservice locally with the `dev` profile:
   ```bash
   cd services/catalog-service
   ./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
   ```
4. Start frontend applications:
   ```bash
   cd apps/customer-web
   npm install && npm run dev
   ```
   Open storefront in browser at `http://localhost:3000`.
