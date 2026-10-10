---
name: security-engineering
description: >-
  Use this skill to evaluate security risks, configure OAuth2/JWT authentication, enforce Role-Based Access Control (RBAC),
  prevent OWASP Top 10 vulnerabilities, protect secrets, and perform threat modeling.
---

# Security Engineering Skill

## 1. Purpose
Enforces defense-in-depth security principles across authentication, authorization, data protection, network communications, secrets management, and audit compliance.

## 2. When to Use It
- Designing or reviewing authentication and token lifecycles (JWT, refresh tokens).
- Configuring Spring Security filter chains and RBAC authorization matrices.
- Reviewing sensitive data handling (passwords, payment webhooks, PII).
- Assessing vulnerabilities (SQL injection, XSS, CSRF, SSRF, IDOR).
- Threat modeling critical workflows (Checkout, Refund, Password Reset).

## 3. Required Inputs
- Identity and role requirements (`docs/08-security/`).
- Threat model definitions (`docs/08-security/threat-model.md`).
- Target API endpoints and user stories.

## 4. Procedure
1. **Authentication Architecture**:
   - Short-lived asymmetric JWT access tokens (15-minute validity) signed with RSA-256 (RS256).
   - Long-lived refresh tokens stored as hashed values in Identity database with rotation on every use.
2. **Authorization & Scopes (RBAC)**:
   - Enforce least privilege: `ROLE_CUSTOMER`, `ROLE_ADMIN`, `ROLE_OPERATOR`, `ROLE_SUPPORT`.
   - Protect against IDOR (Insecure Direct Object Reference): Validate that `customerId` in JWT matches resource owner on all customer endpoints.
3. **Network & Service-to-Service Security**:
   - Mutual TLS (mTLS) for inter-service communication or internal HMAC service tokens.
   - API Gateway verifies JWT before forwarding downstream with `X-User-Id` and `X-User-Roles` headers.
4. **Data Protection & Encryption**:
   - Encrypt PII (email, phone, shipping address) at rest in PostgreSQL using AES-256 where compliance requires.
   - Never store raw card numbers, CVVs, or cardholder credentials (tokenized gateway integration only).
5. **Webhook Security**:
   - Verify cryptographic HMAC signatures (e.g. Stripe/Razorpay webhook signatures) using raw request body before processing.
6. **Publish Security Standards**: Update `docs/08-security/`.

## 5. Rules & Constraints
- NEVER commit secrets, private keys, or passwords to git.
- Passwords must be hashed using BCrypt or Argon2id with work factor >= 12.
- CORS must be restricted to explicit allowed origins, never `*` with credentials.

## 6. Expected Outputs
- Security configurations and RBAC mapping in `docs/08-security/`.
- Automated security integration tests asserting 401/403 responses.

## 7. Validation Checklist
- [ ] Are rate limits applied on sensitive endpoints (Login, OTP, Checkout)?
- [ ] Are all path parameters checked against current user context to prevent IDOR?
- [ ] Are SQL queries strictly parameterized via Spring Data JPA?

## 8. Common Failure Modes
- Exposing actuator endpoints (`/actuator/env`, `/actuator/heapdump`) publicly.
- Validating webhook signatures on parsed JSON rather than raw byte stream.

## 9. Authoritative Links
- [Security Architecture](file:///d:/learning/Microservice/backend/docs/08-security/security-architecture.md)
- [Authentication](file:///d:/learning/Microservice/backend/docs/08-security/authentication.md)
- [Threat Model](file:///d:/learning/Microservice/backend/docs/08-security/threat-model.md)
