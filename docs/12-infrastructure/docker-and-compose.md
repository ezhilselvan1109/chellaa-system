# Docker and Docker Compose Specification

Container definitions, multi-stage Dockerfiles, and compose services.

---

## 1. Multi-Stage Dockerfile Standard

```dockerfile
# Stage 1: Build & Package
FROM eclipse-temurin:21-jdk-alpine AS builder
WORKDIR /app
COPY .mvn/ .mvn/
COPY mvnw pom.xml ./
RUN ./mvnw dependency:go-offline
COPY src/ ./src/
RUN ./mvnw clean package -DskipTests

# Stage 2: Minimal Distroless / Alpine Runtime
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-XX:+UseG1GC", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]
```

---

## 2. Docker Compose Backing Services

- `postgres`: PostgreSQL 16 image exposed on `5432:5432` with healthcheck query `pg_isready`.
- `redis`: Redis 7.2 alpine exposed on `6379:6379`.
- `kafka`: Apache Kafka 3.7 (KRaft mode) exposed on `9092:9092`.
- `opensearch`: OpenSearch 2.13 single-node exposed on `9200:9200`.
- `minio`: MinIO S3 server exposed on `9000:9000` (API) and `9001:9001` (Console).
