# Request and Response Conventions

JSON payloads, naming formats, timestamps, and metadata wrapping standards.

---

## 1. JSON Payload Conventions

- **Casing**: Strict `camelCase` for all JSON properties (e.g. `orderNumber`, `unitPrice`, `taxAmount`).
- **Dates & Times**: ISO-8601 strings in UTC: `"2026-10-10T14:30:00Z"`.
- **Monetary Amounts**: Decimal string or numerical representation with currency object:
  ```json
  "price": {
    "amount": "1499.00",
    "currency": "INR"
  }
  ```
- **IDs**: String representation of UUIDs: `"e8a7c2b3-5491-4cf5-b12a-0bfa7d9e0341"`.

---

## 2. Standard Response Wrapper

### Paginated Collections
```json
{
  "data": [ ... ],
  "meta": {
    "page": 1,
    "size": 20,
    "totalElements": 154,
    "totalPages": 8,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

### Single Resource
Single resource endpoints return the resource object directly at the root (not wrapped in a redundant `{ "data": ... }`) with `Content-Type: application/json`.
