---
name: database-design
description: >-
  Use this skill to model relational and document data stores, enforce strict service database ownership,
  design PostgreSQL schemas, define constraints and indexes, handle concurrency locking, and author migrations.
---

# Database Design Skill

## 1. Purpose
Establishes robust, performant, and resilient data storage architectures, strictly enforcing microservice data ownership, relational integrity, appropriate index selection, and safe migration practices.

## 2. When to Use It
- Designing schema tables, primary keys, foreign keys, and constraints for a microservice.
- Choosing between PostgreSQL (relational default), MongoDB (polymorphic documents), or Redis (cache/ephemeral).
- Writing Flyway or Liquibase versioned migration scripts.
- Preventing database concurrency race conditions (optimistic vs pessimistic locking).
- Designing indexing strategies for high-frequency filter and sort queries.

## 3. Required Inputs
- Service domain aggregates and entities (`docs/03-domain/`).
- Query workload profiles (read-heavy vs write-heavy, RPS targets).
- Service database ownership rules (`docs/05-database/service-database-ownership.md`).

## 4. Procedure
1. **Validate Service Isolation**: Verify that the database belongs strictly to one service. No shared tables or foreign schemas.
2. **Select Database Engine**:
   - PostgreSQL: Default for ACID transactions, orders, inventory, pricing, catalogs.
   - MongoDB: Document workloads only with documented polymorphic schema needs (e.g. notification rendering history, dynamic product spec sheets if justified).
   - Redis: Ephemeral state, rate limits, session tokens, stock reservation TTLs.
3. **Draft Table DDL**:
   - Use UUID v7 or bigint primary keys.
   - Explicit NOT NULL constraints, column comments, and default values.
   - Foreign keys to enforce referential integrity *within* the service database.
4. **Design Indexes**: Create B-Tree indexes for lookup keys, composite indexes for ordered queries (`WHERE status = ? ORDER BY created_at DESC`), and partial indexes for active records.
5. **Formulate Concurrency Strategy**:
   - Optimistic Locking: `@Version` column for catalog items, customer profiles, order states.
   - Pessimistic Locking: `SELECT ... FOR UPDATE` for critical shared counters (e.g. inventory allocations).
6. **Author Migration**: Write sequential Flyway SQL migration script (`V1__init_schema.sql`).
7. **Document Schema**: Publish schema documentation in `docs/05-database/`.

## 5. Rules & Constraints
- Cross-database foreign keys are impossible and strictly prohibited.
- Never store raw unencrypted credentials, passwords, or credit card numbers.
- Always use `TIMESTAMPTZ` (UTC) for datetime columns.

## 6. Expected Outputs
- Schema definitions and index justifications in `docs/05-database/`.
- Testcontainers migration test validating script execution.

## 7. Validation Checklist
- [ ] Are all foreign keys indexed to prevent table lock escalations?
- [ ] Does every table have `created_at` and `updated_at` audit columns?
- [ ] Are migration scripts backward-compatible for blue-green rolling deploys?

## 8. Common Failure Modes
- Missing indexes on foreign keys causing sequential table scans under load.
- Performing long HTTP calls inside an open database transaction block.
- Inconsistent timezone handling causing reporting discrepancies.

## 9. Authoritative Links
- [Database Selection](file:///d:/learning/Microservice/backend/docs/05-database/database-selection.md)
- [PostgreSQL Design](file:///d:/learning/Microservice/backend/docs/05-database/postgresql-design.md)
- [Database Migrations](file:///d:/learning/Microservice/backend/docs/05-database/database-migrations.md)
