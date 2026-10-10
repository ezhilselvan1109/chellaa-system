# Database Backup and Disaster Recovery

Policies for automated backups, WAL archiving, point-in-time recovery (PITR), and disaster recovery drills.

---

## 1. Backup Policies & RPO / RTO Targets

- **Recovery Point Objective (RPO)**: $\le 5$ minutes (Maximum allowable data loss).
- **Recovery Time Objective (RTO)**: $\le 60$ minutes (Maximum allowable time to restore full operations).

---

## 2. Backup Mechanisms

1. **Continuous Write-Ahead Log (WAL) Archiving**:
   - PostgreSQL WAL segments are streamed continuously to redundant S3 storage (`pg_wal` shipping / AWS Aurora continuous backup).
2. **Daily Snapshot Backups**:
   - Full disk-level snapshots taken daily at `02:00 UTC` with a 30-day retention window.
3. **Point-in-Time Recovery (PITR)**:
   - Enables restoring the database to any specific millisecond within the past 30 days to recover from human operator error or malicious data corruption.
4. **Disaster Recovery Drills**:
   - Automated sandbox restoration drill executed monthly to verify backup image integrity.
