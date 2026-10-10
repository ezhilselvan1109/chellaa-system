# Error Handling (RFC 7807 Problem Details)

All microservices adhere to `RFC 7807 Problem Details for HTTP APIs` (`application/problem+json`) for error responses.

---

## 1. RFC 7807 Standard Error Envelope

```json
{
  "type": "https://api.store.com/errors/insufficient-stock",
  "title": "Insufficient Stock",
  "status": 409,
  "detail": "Requested quantity (5) exceeds available stock (2) for SKU 'TSHIRT-NAVY-L'.",
  "instance": "/api/v1/orders/checkout",
  "errorCode": "INSUFFICIENT_STOCK",
  "timestamp": "2026-10-10T14:35:10Z",
  "invalidParams": [
    {
      "field": "items[0].quantity",
      "reason": "Must not exceed available stock of 2"
    }
  ]
}
```

---

## 2. Standard HTTP Error Status Codes

| HTTP Status | Error Type | Scenario |
| :--- | :--- | :--- |
| `400 Bad Request` | `VALIDATION_FAILED` | Malformed JSON, constraint violation (`@NotNull`, `@Size`). |
| `401 Unauthorized` | `AUTHENTICATION_REQUIRED` | Missing or expired JWT access token. |
| `403 Forbidden` | `INSUFFICIENT_PERMISSIONS`| Valid token but lacking required role (e.g. non-admin accessing admin route). |
| `404 Not Found` | `RESOURCE_NOT_FOUND` | Referenced order ID, product ID, or customer ID does not exist. |
| `409 Conflict` | `STATE_CONFLICT` | Optimistic lock failure, duplicate unique constraint, or stock race condition. |
| `422 Unprocessable`| `BUSINESS_RULE_VIOLATION`| Input is syntactically valid but breaks business rule (e.g. coupon expired). |
| `429 Too Many Req` | `RATE_LIMIT_EXCEEDED` | Request throttled by Redis token bucket. |
| `500 Internal Error`| `INTERNAL_SERVER_ERROR` | Unexpected unhandled exception (stack traces omitted from client response). |
