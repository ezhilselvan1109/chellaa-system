# Authentication Architecture

Customer identity verification, password hashing, Multi-Factor Authentication (MFA), and session establishment.

---

## 1. Authentication Mechanisms

1. **Customer Authentication**:
   - Primary: Mobile Number + SMS OTP (6 digits, 10-minute expiry, max 3 attempts).
   - Secondary: Email + Password (minimum 8 characters, complex character rules).
   - Passwords hashed with BCrypt (cost factor 12) or Argon2id.
2. **Staff / Administrator Authentication**:
   - Work email + Strong Password + Mandatory Time-based One-Time Password (TOTP via Google Authenticator / Authy).
3. **Session Establishment**:
   - Successful authentication issues a signed RS256 JWT access token and sets an opaque refresh token in an `HttpOnly`, `Secure`, `SameSite=Strict` cookie.
