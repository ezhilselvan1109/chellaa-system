# Database Indexing Strategy

Indexing rules, index types, and query optimization guidelines.

---

## 1. Indexing Principles

1. **Index Every Foreign Key**: PostgreSQL does NOT automatically index foreign key columns. Unindexed foreign keys cause full table locks during parent row deletions.
2. **Composite Indexes (Left-Prefix Rule)**:
   - Queries filtering by status and sorting by date: `CREATE INDEX idx_orders_customer_date ON orders(customer_id, created_at DESC)`.
3. **Partial Indexes for Hot Queues**:
   - Outbox Polling: `CREATE INDEX idx_outbox_pending ON outbox_messages(created_at) WHERE status = 'PENDING'`. Only unread messages are indexed, keeping the index size tiny (<1MB).
4. **GIN Indexes for JSONB**:
   - `CREATE INDEX idx_product_attrs ON products USING gin(attributes_jsonb)`.

---

## 2. EXPLAIN ANALYZE Review Protocol

Before committing a new query in JPA repositories:
- Run `EXPLAIN (ANALYZE, BUFFERS) <query>` against a representative dataset.
- Confirm cost < 500 and execution time < 10ms.
- Verify execution uses `Index Scan` or `Bitmap Index Scan`, not `Seq Scan`.
