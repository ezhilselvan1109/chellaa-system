# Audit Logging

Immutable recording of sensitive business, financial, and administrative operations.

---

## 1. Auditable Actions

Every state modification triggered by staff or automated cron must emit an immutable audit log entry:
- Administrative price adjustments, coupon creation, stock write-offs.
- Manual order cancellations, returns approval, refund payouts.
- Role assignments, privilege escalations, customer account lockouts.

---

## 2. Audit Record Schema

```sql
CREATE TABLE administrative_audit_logs (
    audit_id UUID PRIMARY KEY,
    actor_id UUID NOT NULL,
    actor_email VARCHAR(255) NOT NULL,
    actor_role VARCHAR(50) NOT NULL,
    action VARCHAR(100) NOT NULL, -- e.g., REFUND_APPROVED, STOCK_ADJUSTED
    target_resource VARCHAR(100) NOT NULL,
    target_resource_id VARCHAR(100) NOT NULL,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    diff_before JSONB,
    diff_after JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```
Logs are append-only; update and delete privileges are revoked from application database roles.
