# Threat Model (STRIDE)

STRIDE threat modeling analysis across critical e-commerce flows.

---

## 1. STRIDE Threat Analysis

| Threat Category | Potential Attack Vector | Applied Mitigation |
| :--- | :--- | :--- |
| **Spoofing** | Attacker spoofs customer identity via brute-force OTP. | Rate-limit OTP requests (max 3 attempts per 10m); lockout on repeated failures; 6-digit cryptographic entropy. |
| **Tampering** | Attacker intercepts checkout POST request and reduces order total from ₹2,000 to ₹1. | Order total is never accepted from client input; Order Service computes total server-side via Pricing Service. |
| **Repudiation** | Customer claims they never authorized an order. | IP address, User-Agent, and verified 3D-Secure gateway authentication recorded in immutable order snapshot. |
| **Information Disclosure** | IDOR vulnerability exposes other customers' order history or home address. | Gateway and microservice method-level checks verify `customerId` claim matches resource owner. |
| **Denial of Service** | Bot script exhausts inventory stock by initiating checkouts without paying. | 15-minute strict reservation TTL; CAPTCHA on checkout; Redis token bucket rate limit per IP. |
| **Elevation of Privilege** | Storefront customer tampers with JWT to add `ROLE_ADMIN`. | Asymmetric RS256 signature verification ensures modified JWTs fail signature check immediately. |
