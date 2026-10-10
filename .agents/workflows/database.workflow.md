# Database Workflow

## 1. Purpose & Objective
Ensure disciplined data architecture, strict per-service data ownership, optimized schema design, safe migrations, correct concurrency locking, and performant indexing across all data stores.

## 2. Prerequisites & Inputs
- Service domain model and aggregate definitions.
- Expected read/write query patterns and workload volume.
- Service database ownership guidelines.

## 3. Workflow Sequence

```text
Data Requirements & Domain Model
                |
                v
Service Ownership Validation (Strict Isolation)
                |
                v
Workload, Concurrency & Query Patterns
                |
                v
Technology Selection (PostgreSQL vs MongoDB vs Redis)
                |
                v
Physical Schema / Document Model Design
                |
                v
Constraints, Keys & Indexing Strategy
                |
                v
Concurrency Control (Pessimistic vs Optimistic Locking)
                |
                v
Migration Scripts Creation (Flyway / Liquibase)
                |
                v
Database Integration Testing (Testcontainers)
                |
                v
Query Plan & Performance Review (EXPLAIN ANALYZE)
                |
                v
Documentation & Schema Publishing
```

## 4. Responsible Skills
- `database-design`
- `domain-design`
- `springboot-development`
- `testing-engineering`
- `performance-engineering`

## 5. Completion Gate
- Schema changes are written as reproducible, forward-only Flyway/Liquibase migration scripts.
- Foreign keys and unique constraints protect relational data integrity.
- Indexes exist for all high-frequency filter and join keys; zero unindexed sequential table scans.
- Concurrency locks (e.g. optimistic locking via `@Version` or pessimistic lock on inventory allocations) prevent race conditions.
- Testcontainers migration test executes cleanly.
