# JWT and Refresh Token Lifecycle

Specifications for token structures, cryptographic signing, expiration, and rotation.

---

## 1. Token Lifecycles & Cryptography

1. **Access Token (JWT)**:
   - Algorithm: RS256 (RSA Signature with SHA-256).
   - Lifetime: **15 minutes**.
   - Storage: Stored in frontend memory (React state / closure), never in localStorage.
   - Claims:
     ```json
     {
       "iss": "https://api.store.com/auth",
       "sub": "c1d2e3f4-a5b6-7c8d-9e0f-1a2b3c4d5e6f",
       "roles": ["ROLE_CUSTOMER"],
       "email": "customer@example.com",
       "iat": 1791642600,
       "exp": 1791643500
     }
     ```
2. **Refresh Token**:
   - Format: Opaque cryptographically secure 256-bit random string.
   - Lifetime: **30 days**.
   - Storage: Set as `HttpOnly`, `Secure`, `SameSite=Strict` cookie path `/api/v1/auth/refresh`.
   - Single-Use Rotation: Every exchange issues a new refresh token and immediately invalidates the old token in PostgreSQL (`refresh_tokens` table). If a previously used refresh token is presented, all sessions for that user are revoked immediately (Token Theft Detection).
