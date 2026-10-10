# API Security

Hardening against OWASP API Security Top 10 vulnerabilities.

---

## 1. Threat Mitigations

| OWASP Vulnerability | Architecture Mitigation |
| :--- | :--- |
| **API1: Broken Object Level Authorization (BOLA/IDOR)** | Method-level security verifying that `#customerId` from token matches requested resource owner. |
| **API2: Broken Authentication** | Asymmetric RS256 JWTs with 15-minute expiration; rotating refresh tokens stored in HttpOnly cookies. |
| **API3: Broken Object Property Level Auth** | Strict DTO projection classes; JPA entities are never deserialized directly from request bodies. |
| **API4: Unrestricted Resource Consumption** | Redis token bucket rate limiting; maximum pagination page size capped at 100. |
| **API5: Broken Function Level Authorization** | Administrative endpoints (`/api/v1/admin/**`) require `ROLE_ADMIN` evaluated at Gateway and Service layers. |
| **API6: Unrestricted Access to Sensitive Business Flows**| Captcha / SMS OTP verification required on account registration and password reset. |
| **API7: Server-Side Request Forgery (SSRF)** | Media upload endpoints accept only pre-signed S3 URLs; outbound HTTP requests to external domains are prohibited. |
| **API8: Security Misconfiguration** | CORS restricted to storefront and admin domains; stack traces suppressed in production error responses. |
| **API9: Improper Inventory Management** | Centralized OpenAPI docs; deprecated API versions actively decommissioned after 6 months. |
| **API10: Unsafe Consumption of APIs** | Gateway verifies SSL certificates and validates HMAC signatures on payment/carrier webhooks. |
