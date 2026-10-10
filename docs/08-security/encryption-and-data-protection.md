# Encryption and Data Protection

Cryptography standards, data at rest, data in transit, and PII protection.

---

## 1. Cryptography Standards

- **In Transit**: TLS 1.3 mandated across all public and private network boundaries. Cipher suites restricted to forward-secret AEAD ciphers (`TLS_AES_256_GCM_SHA384`, `TLS_CHACHA20_POLY1305_SHA256`).
- **At Rest**:
  - PostgreSQL disks encrypted via AWS KMS / LUKS AES-256.
  - S3 buckets encrypted with Server-Side Encryption (SSE-S3 / SSE-KMS).
  - High-sensitivity customer PII fields (national tax IDs, phone numbers) column-encrypted using JPA converters with AES-256-GCM.

---

## 2. Tokenized Payment Redirection

Raw payment cards are never handled directly by backend servers. Storefront clients communicate directly with payment gateway iframes, exchanging card numbers for opaque, non-reversible tokens (`tok_123456`).
