# Service-to-Service Security

Inter-service authentication, Mutual TLS (mTLS), and internal HMAC tokens.

---

## 1. Zero Trust Internal Mesh

1. **Mutual TLS (mTLS)**:
   - All internal HTTP and Kafka communications traverse an encrypted mTLS tunnel managed via Istio or private TLS certificates.
   - Client and server exchange and verify X.509 certificates signed by the internal cluster Certificate Authority (CA).
2. **Internal Service Tokens**:
   - Synchronous inter-service REST calls carry an internal signed HMAC token: `X-Internal-Service-Auth: <JWT_SERVICE_TOKEN>`.
   - Microservices verify that calls originate from authorized peer pods rather than rogue cluster processes.
