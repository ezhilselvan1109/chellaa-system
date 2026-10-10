---
name: api-design
description: >-
  Use this skill to design consistent RESTful APIs, OpenAPI 3.1 specifications, request/response DTOs,
  RFC 7807 error responses, idempotency mechanisms, pagination, and RBAC authorization schemes.
---

# API Design Skill

## 1. Purpose
Defines enterprise RESTful API contracts that provide seamless developer experience, strong typing, strict backward compatibility, standardized error envelopes, and built-in idempotency.

## 2. When to Use It
- Defining external or internal HTTP endpoints for microservices.
- Drafting OpenAPI 3.1 specifications.
- Setting pagination, sorting, and filtering standards.
- Specifying `Idempotency-Key` headers and deduplication contracts.
- Documenting endpoint authorization roles (`ROLE_CUSTOMER`, `ROLE_ADMIN`, `ROLE_OPERATIONS`).

## 3. Required Inputs
- Service domain boundaries and user stories.
- Security scopes and RBAC definitions (`docs/08-security/`).
- Established API standards (`docs/06-api/api-standards.md`).

## 4. Procedure
1. **URI Path Design**:
   - Use plural nouns: `/api/v1/products`, `/api/v1/orders/{orderId}/items`.
   - Never use verbs in paths (e.g. use `POST /api/v1/orders/{orderId}/cancel` as a state transition sub-resource or `POST /api/v1/orders/{id}/cancellations`).
2. **HTTP Method Semantics**:
   - `GET`: Safe, idempotent read.
   - `POST`: Non-idempotent creation or command.
   - `PUT`: Complete idempotent replacement.
   - `PATCH`: Partial modification.
   - `DELETE`: Idempotent removal.
3. **Draft Request & Response Models**:
   - Explicit JSON attribute naming (`camelCase`).
   - Validate payloads with Jakarta Bean Validation (`@NotNull`, `@Size`, `@Min`).
4. **Error Handling Architecture**:
   - Adhere to `RFC 7807 Problem Details for HTTP APIs`: `type`, `title`, `status`, `detail`, `instance`, `invalidParams`.
5. **Idempotency Strategy**:
   - Require `Idempotency-Key` header on financial/mutation requests (`POST /api/v1/orders/checkout`, `POST /api/v1/payments`).
6. **Pagination & Filtering**:
   - Keyset or cursor pagination for large datasets; `page` and `size` with max cap (default 20, max 100) for UI listings.
7. **Publish Contract**: Update `docs/06-api/` with detailed request/response examples and OpenAPI schemas.

## 5. Rules & Constraints
- Never leak database internal IDs, stack traces, or SQL exception messages to clients.
- Never introduce breaking changes to existing v1 endpoints without introducing a v2 version path.
- All mutating endpoints must return semantic HTTP status codes (`201 Created` with `Location` header, `200 OK`, `202 Accepted`, `204 No Content`).

## 6. Expected Outputs
- OpenAPI 3.1 specification files in `docs/06-api/`.
- Concrete sample payloads for success and failure cases.

## 7. Validation Checklist
- [ ] Are all path parameters and query parameters clearly typed?
- [ ] Does the response include ISO-8601 UTC timestamps (`YYYY-MM-DDTHH:mm:ssZ`)?
- [ ] Are 401 Unauthorized vs 403 Forbidden scenarios explicitly handled?

## 8. Common Failure Modes
- Returning HTTP 200 OK with `{ "success": false, "error": "failed" }`.
- Unbounded pagination queries allowing clients to request `size=1000000`.
- Inconsistent casing (mixing snake_case and camelCase in JSON responses).

## 9. Authoritative Links
- [API Standards](file:///d:/learning/Microservice/backend/docs/06-api/api-standards.md)
- [Error Handling](file:///d:/learning/Microservice/backend/docs/06-api/error-handling.md)
- [Idempotency](file:///d:/learning/Microservice/backend/docs/06-api/idempotency.md)
