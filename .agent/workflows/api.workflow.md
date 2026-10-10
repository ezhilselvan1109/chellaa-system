# API Workflow

## 1. Purpose & Objective
Design, document, implement, test, and version consistent RESTful APIs adhering to enterprise standards, strict error formatting, idempotency, and security constraints.

## 2. Prerequisites & Inputs
- Service domain boundaries and business capability.
- Security and authorization requirements (scopes/roles).
- Client query patterns (Customer Web vs Admin Web).

## 3. Workflow Sequence

```text
Use Case & Business Capability
               |
               v
Authorization & RBAC Requirements
               |
               v
Request / Response Contract Definition
               |
               v
Validation Rules & Error Model (RFC 7807)
               |
               v
Idempotency (Idempotency-Key) & Concurrency
               |
               v
OpenAPI 3.1 Specification Draft
               |
               v
Spring MVC Controller & DTO Implementation
               |
               v
Contract & Integration Testing (MockMvc / WebTestClient)
               |
               v
API Documentation & Client SDK Generation
```

## 4. Responsible Skills
- `api-design`
- `security-engineering`
- `springboot-development`
- `testing-engineering`

## 5. Completion Gate
- Endpoints follow REST conventions (nouns, plural resources, semantic HTTP methods).
- Error responses follow `RFC 7807` Problem Details structure.
- State-mutating POST/PUT/PATCH endpoints support `Idempotency-Key` headers where critical.
- MockMvc / integration tests verify status codes, payload validation, and unauthorized access (401/403).
- OpenAPI spec published to `docs/06-api/`.
