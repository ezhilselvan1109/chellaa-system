# OpenAPI Strategy and Documentation

Standards for OpenAPI 3.1 documentation, code generation, and contract publishing.

---

## 1. SpringDoc OpenAPI 3.1 Configuration

Every microservice includes `springdoc-openapi-starter-webmvc-ui` configured to automatically generate OpenAPI 3.1 specifications:
- Public Swagger UI exposed locally at `/swagger-ui.html`.
- Machine-readable specification exposed at `/v3/api-docs.yaml` and `/v3/api-docs.json`.
- Aggregated gateway documentation: API Gateway consolidates downstream microservice OpenAPI specs into a unified portal at `/swagger-ui.html`.

---

## 2. API Contract File Layout

Service-specific contracts are stored in `docs/06-api/` for reference and frontend client generation:
- `order-service-api.md`
- `catalog-service-api.md`
- `inventory-service-api.md`
- `payment-service-api.md`
