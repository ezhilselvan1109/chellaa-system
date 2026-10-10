# Secrets and Environment Segregation

Secret rotation, vault bindings, and multi-environment boundary separation.

---

## 1. Environment Segregation Rules

- **Zero Credential Sharing**: Credentials (database passwords, gateway API keys, webhook secrets) used in staging must NEVER be valid or reused in production environments.
- **Dedicated Service Accounts**: Microservices use IAM roles for service accounts (IRSA on AWS EKS) to assume fine-grained cloud permissions without long-lived static AWS access keys.
- **Automated Rotation**: Database credentials rotate automatically every 90 days via HashiCorp Vault / AWS Secrets Manager with zero downtime connection restarts.
