# API Testing Standards

REST endpoint verification using MockMvc and WebTestClient.

---

## 1. Controller Slice & Security Verification

API tests verify:
1. **Payload Serialization & Deserialization**: Jakarta Bean Validation triggers HTTP 400 with `RFC 7807` error details on invalid inputs.
2. **Security & Role Enforcement**:
   - Requests without Bearer token return `401 Unauthorized`.
   - Customer token accessing `/api/v1/admin/**` returns `403 Forbidden`.
   - Customer token accessing a different customer's order ID returns `403 Forbidden` (IDOR check).
3. **Idempotency Execution**:
   - Duplicate request with identical `Idempotency-Key` returns cached response without duplicate side effects.
