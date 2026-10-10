# Analytical Aggregation and Reconciliation

Daily financial reconciliation cron jobs and discrepancy auditing.

---

## 1. Midnight Financial Reconciliation Job

Every night at `01:00 UTC`:
1. The `reporting-service` initiates a read-only batch reconciliation job:
   - Sums all order totals in `order_db` completed between `00:00:00` and `23:59:59 UTC`.
   - Sums all gateway settlement webhooks in `payment_db`.
   - Sums all refund ledger debits in `refund_db`.
2. Compares totals against materialized rows in `daily_sales_summary`.
3. If variance exceeds ₹0.00:
   - Generates an automated discrepancy alert to the finance team.
   - Self-corrects `reporting_db` materialized rows to match the authoritative transactional ledgers.
