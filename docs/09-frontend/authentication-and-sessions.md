# Frontend Authentication and Sessions

Silent token refresh, in-memory token storage, and session termination.

---

## 1. Zero Token Storage in LocalStorage

To defend against Cross-Site Scripting (XSS) token theft:
- The RS256 JWT access token is stored strictly in JavaScript runtime memory (`authStore`).
- The refresh token is stored exclusively in a secure `HttpOnly` cookie inaccessible to JavaScript.
- On browser tab refresh, an initial `POST /api/v1/auth/refresh` silently restores the access token before mounting protected routes.

---

## 2. Session Revocation & Multi-Tab Sync

- When a customer logs out, `POST /api/v1/auth/logout` invalidates the server-side refresh token.
- A `BroadcastChannel` or `window.addEventListener('storage')` event synchronizes the logout action across all open browser tabs simultaneously.
