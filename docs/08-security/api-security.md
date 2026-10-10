# API Security Architecture (Security Domain Specification)

Mitigations against the OWASP API Security Top 10 within the security architecture context.

---

## 1. Gateway and Edge Defense

1. **Edge Protection (WAF & DDoS Mitigation)**:
   - Ingress WAF enforces geo-blocking, anomalous bot signature detection, and IP reputation scoring.
   - Global rate limiting: 100 requests per minute per IP for public browsing; 10 requests per minute for authentication and checkout.
2. **Transport Security**:
   - Enforce TLS 1.3 only. Plain HTTP requests are rejected or redirected with HTTP 301.

---

## 2. API Threat Defenses

- **Broken Object Level Authorization (BOLA)**: Validates resource ownership against `#customerId` in JWT context.
- **Broken Authentication**: Ephemeral RS256 JWT access tokens (15m) paired with single-use rotating refresh tokens stored in HttpOnly cookies.
- **Mass Assignment**: Spring Boot DTOs with explicit setters/records; internal JPA entity models are never bound directly to HTTP request bodies.
- **Injection Attacks**: Parameterized queries via Spring Data JPA; Hibernate criteria builders; strict regex validation on inputs.
- **SSRF**: Media endpoints strictly issue AWS S3 pre-signed upload URLs; outbound fetching of user-supplied URLs is prohibited.
