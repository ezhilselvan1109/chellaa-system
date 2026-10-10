# Secrets Management

Guidelines for environment variables, secret vaults, and zero secret leakage.

---

## 1. Secrets Governance Rules

1. **Strictly No Hardcoded Secrets**:
   - Zero credentials, database passwords, private keys, or API tokens permitted in git repositories.
2. **Runtime Injection**:
   - Local Development: Injected via `.env` files explicitly added to `.gitignore`.
   - Production / Kubernetes: Injected into pod environment variables via Kubernetes Secrets backed by HashiCorp Vault or AWS Secrets Manager.
3. **Automated Secret Scanning**:
   - CI/CD pipelines run pre-commit hooks and Gitleaks scanning to block commits containing high-entropy strings or private key formats.
