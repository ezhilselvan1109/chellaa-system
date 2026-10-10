# Read Model Backfill and Rebuild Procedures

Procedures for completely rebuilding reporting tables from Kafka or database snapshots.

---

## 1. Rebuild Procedure

If reporting schemas evolve (e.g. adding new tax dimensions):
1. Create new table `daily_sales_summary_v2`.
2. Execute historical backfill job:
   - Option A: Replay Kafka topics from earliest available offset (`--to-earliest`).
   - Option B: Execute read-only batch extraction from `order_db` and `refund_db` historical tables.
3. Verify summary totals match financial accounting records.
4. Rename `daily_sales_summary_v2` to active production table.
