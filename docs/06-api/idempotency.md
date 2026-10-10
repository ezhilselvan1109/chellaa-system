# API Idempotency Specification

Contract specifications for the `Idempotency-Key` HTTP header.

---

## 1. Specification & Protocol

- **Header Name**: `Idempotency-Key`
- **Format**: Standard UUID v4 or v7 (e.g. `b4a1c6e2-9f33-4f21-897b-94c637a8b111`).
- **Required Endpoints**:
  - `POST /api/v1/orders/checkout`
  - `POST /api/v1/payments`
  - `POST /api/v1/refunds`
  - `POST /api/v1/returns`

---

## 2. Server Behavior & Status Codes

1. **Initial Processing**:
   - Status: HTTP 201 Created or 200 OK.
   - Body cached in Redis with TTL 60 seconds along with status code and headers.
2. **Duplicate Request Arrives (While First Request is Still Processing)**:
   - Server returns: `HTTP 409 Conflict` with `RFC 7807` error code `IDEMPOTENT_REQUEST_IN_FLIGHT`.
3. **Duplicate Request Arrives (After First Request Completed)**:
   - Server returns: Identical cached HTTP response payload, status code, and header `X-Cache-Lookup: HIT-IDEMPOTENCY`.
