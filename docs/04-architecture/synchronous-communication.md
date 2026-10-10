# Synchronous Communication

Standards and resilience mechanisms for inter-service synchronous HTTP/REST communications.

---

## 1. REST Conventions & Client Resilience

1. **Client Library**:
   - Microservices use Spring 6 `RestClient` or `WebClient` with HTTP connection pooling via Apache HttpComponents HttpClient 5.
2. **Resilience4j Integration**:
   - Every outbound HTTP call is guarded by Resilience4j decorators:
     - **Connect Timeout**: 1,000 ms.
     - **Read Timeout**: 3,000 ms.
     - **Retry Policy**: 2 retries on HTTP 503 or `ConnectException` with exponential backoff (100ms, 250ms). Retries are prohibited on non-idempotent POST requests unless an `Idempotency-Key` is passed.
     - **Circuit Breaker**: Opens if 50% of requests fail over a 50-request sliding window, shedding traffic for 10 seconds before half-open probe.

## 2. Distributed Context Propagation

All synchronous inter-service calls forward critical tracing and security context headers:
- `traceparent`: W3C distributed trace header (`00-<trace-id>-<span-id>-01`).
- `X-Correlation-Id`: Client request identifier for unified log filtering.
- `X-User-Id`: Authenticated user UUID extracted by the API Gateway.
- `X-User-Roles`: Comma-separated user roles (e.g. `ROLE_CUSTOMER`).
