# Authorization and Access Control

Authorization policies, ABAC/RBAC models, and Insecure Direct Object Reference (IDOR) prevention.

---

## 1. Authorization Model

The platform enforces Role-Based Access Control (RBAC) augmented with Attribute-Based Access Control (ABAC) ownership rules:
1. **RBAC**: Checks if the user holds the coarse role required for an operation (`ROLE_CUSTOMER`, `ROLE_ADMIN`, `ROLE_SUPPORT`).
2. **ABAC Ownership Validation**: For customer-owned resources (orders, addresses, reviews), the service validates that the authenticated customer ID matches the resource owner:
   ```java
   if (!order.getCustomerId().equals(currentAuthenticatedUser.getId())) {
       throw new AccessDeniedException("Access to foreign order prohibited");
   }
   ```
   This prevents horizontal privilege escalation (IDOR attacks).
