# PostgreSQL Infrastructure Deployment

Deployment topologies for development and production environments.

---

## 1. Environments

- **Development (Docker Compose)**: Single container mounting local named volume `pgdata:/var/lib/postgresql/data`. Automatic database creation script initializes logical databases.
- **Production (AWS Aurora PostgreSQL / Kubernetes StatefulSet)**:
  - Multi-AZ deployment across 3 availability zones.
  - Dedicated Primary Writer instance + 2 Read Replicas with automated failover in $<30$ seconds.
  - Automatic storage auto-scaling up to 64TB.
  - PgBouncer deployed as connection pooler proxy.
