# Frontend Security Standards

Mitigations for XSS, CSRF, Clickjacking, and Content Security Policy (CSP).

---

## 1. Threat Mitigations in the Browser

1. **Cross-Site Scripting (XSS)**:
   - React's default JSX string escaping prevents script injection.
   - Prohibit `dangerouslySetInnerHTML` unless explicitly sanitized via DOMPurify.
2. **Cross-Site Request Forgery (CSRF)**:
   - Refresh token cookies set with `SameSite=Strict` and `Secure=true`.
   - Mutation endpoints require custom headers (`Authorization: Bearer ...` or `X-Requested-With`), which browsers block cross-origin requests from setting without CORS preflight approval.
3. **Content Security Policy (CSP)**:
   - Enforce HTTP headers:
     `Content-Security-Policy: default-src 'self'; script-src 'self' https://checkout.razorpay.com; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; img-src 'self' data: https://*.s3.amazonaws.com; connect-src 'self' https://api.store.com https://api.razorpay.com;`
4. **Clickjacking Defense**:
   - `X-Frame-Options: DENY` on storefront; `X-Frame-Options: SAMEORIGIN` on admin portal.
