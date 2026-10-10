# Database Architecture

Logical and physical topology, database isolation levels, and replication strategies.

---

## 1. Logical vs. Physical Database Isolation

1. **Logical Isolation (Development & Staging)**:
   - A single shared PostgreSQL cluster hosts isolated logical databases (`identity_db`, `catalog_db`, `order_db`, etc.).
   - Distinct database users per microservice; each user is granted permissions strictly on its dedicated database.
2. **Physical Isolation (Production)**:
   - High-throughput services (Order Service, Inventory Service, Catalog Service) run on dedicated managed PostgreSQL instances (e.g. AWS Aurora or dedicated Kubernetes StateFulSets).
   - Read replicas provisioned for Catalog and Pricing services.

---

## 2. Global Standards for Relational Tables

Every relational table created across all services must comply with:
- **Primary Keys**: UUID v7 (time-ordered, highly index-friendly) or BIGSERIAL.
- **Audit Columns**: `created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP`, `updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP`.
- **Soft Deletion**: `deleted_at TIMESTAMPTZ` (where regulatory compliance or history preservation mandates).
- **Optimistic Locking**: `version INT NOT NULL DEFAULT 0` for entity roots mutated concurrently.
