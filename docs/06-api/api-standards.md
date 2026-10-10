# API Standards

Universal REST API standards, naming conventions, and HTTP status code semantics.

---

## 1. RESTful URI Conventions

1. **Plural Nouns for Resources**:
   - `/api/v1/products`, `/api/v1/orders`, `/api/v1/customers`.
   - Never use verbs in URIs (e.g. avoid `/api/v1/getProducts` or `/api/v1/createOrder`).
2. **Sub-Resources for Relationships**:
   - `/api/v1/orders/{orderId}/items`
   - `/api/v1/customers/{customerId}/addresses`
3. **State Transition Commands**:
   - Model state transitions as sub-resource commands or actions:
     - `POST /api/v1/orders/{orderId}/cancellations`
     - `POST /api/v1/returns/{returnId}/approvals`

---

## 2. HTTP Method Semantics

| HTTP Method | Action | Idempotent | Safe | Expected Success Status |
| :--- | :--- | :--- | :--- | :--- |
| `GET` | Read resource | Yes | Yes | `200 OK` |
| `POST` | Create resource / execute command | No | No | `201 Created` with `Location` header or `200 OK` |
| `PUT` | Complete resource replacement | Yes | No | `200 OK` |
| `PATCH` | Partial resource update | No | No | `200 OK` |
| `DELETE` | Remove resource | Yes | No | `204 No Content` |
