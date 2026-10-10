# Data Retention and Archiving

Statutory data retention schedules, cold-storage archiving, and PII anonymization.

---

## 1. Statutory Retention Schedules

| Data Entity | Retention Period | Storage Tier | Legal Rationale |
| :--- | :--- | :--- | :--- |
| **Tax Invoices & Credit Notes** | 8 Years | S3 Glacier (WORM compliant) | GST / Tax statutory audit compliance |
| **Completed Orders** | 7 Years | PostgreSQL -> S3 Parquet Archive | Commercial contract statute of limitations |
| **Payment Transactions** | 7 Years | PostgreSQL -> Cold Archive | Banking & anti-money laundering records |
| **Outbox Message Logs** | 14 Days | Auto-purged from PostgreSQL | Ephemeral delivery buffer |
| **Anonymous Guest Carts** | 7 Days | Redis TTL auto-expiration | Temporary session state |
| **Search Query Logs** | 90 Days | OpenSearch ILM rollover | Search analytics & tuning |

---

## 2. Automated Purge & Cold Archival Jobs

- Spring Batch / cron jobs run weekly to move orders older than 2 years into compressed Parquet files stored on S3.
- PostgreSQL partitions older than 2 years are cleanly detached and archived, maintaining sub-100GB table sizes for operational databases.
