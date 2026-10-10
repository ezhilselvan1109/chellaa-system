# Authentication and Authorization in APIs

Gateway-level authentication, JWT verification, and Role-Based Access Control (RBAC).

---

## 1. Authentication Flow

1. **Bearer Token Header**:
   - Authenticated client requests must pass an HTTP header: `Authorization: Bearer <JWT_ACCESS_TOKEN>`.
2. **API Gateway Role**:
   - API Gateway verifies JWT cryptographic signature using RS256 public key.
   - Gateway unpacks claims and injects downstream headers to private microservices:
     - `X-User-Id`: Customer or Admin UUID.
     - `X-User-Email`: User email.
     - `X-User-Roles`: Comma-separated roles (e.g. `ROLE_CUSTOMER`, `ROLE_ADMIN`).

---

## 2. Microservice Role-Based Access Control (RBAC)

Spring Security evaluates permissions using method-level security:
```java
@PreAuthorize("hasAuthority('ROLE_ADMIN') or (hasAuthority('ROLE_CUSTOMER') and #customerId == authentication.principal.id)")
@GetMapping("/api/v1/customers/{customerId}/orders")
public ResponseEntity<List<OrderDto>> getCustomerOrders(@PathVariable UUID customerId) {
    // ...
}
```
Prevents Insecure Direct Object Reference (IDOR) attacks by asserting token ownership against path parameters.
