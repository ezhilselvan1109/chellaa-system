# Database Migrations Architecture

Rules and practices for Flyway migrations, versioning, and zero-downtime schema evolution.

---

## 1. Migration File Conventions (Flyway)

All SQL migrations reside in `src/main/resources/db/migration/` within each microservice:
- Naming format: `V<Version>__<Description>.sql` (e.g. `V1__init_schema.sql`, `V2__add_carrier_awb_column.sql`).
- Repeatable migrations: `R__<Procedure_or_View>.sql`.

---

## 2. Zero-Downtime Migration Rules (Expand and Contract)

To allow rolling deployments without downtime:
1. **Never Rename Columns in a Single Step**:
   - *Phase 1 (Expand)*: Add new column; deploy code that writes to both old and new columns.
   - *Phase 2 (Migrate)*: Run background script backfilling data from old to new column.
   - *Phase 3 (Switch)*: Deploy code that reads and writes exclusively to the new column.
   - *Phase 4 (Contract)*: Drop old column in a subsequent release.
2. **Never Add NOT NULL Without Default**:
   - Adding a NOT NULL constraint on existing tables locks tables; add column as nullable, populate default, then add constraint.
3. **Index Creation**:
   - In production PostgreSQL, always create indexes concurrently: `CREATE INDEX CONCURRENTLY IF NOT EXISTS`.
