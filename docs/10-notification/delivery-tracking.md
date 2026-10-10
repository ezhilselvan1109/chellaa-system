# Delivery Tracking Architecture

Message lifecycle state machine, provider status updates, and tracking tables.

---

## 1. Message State Machine

```text
PENDING -> ACCEPTED -> SENT -> DELIVERED -> READ
    |           |        |          |
    +-----------+--------+----------+---> FAILED
```

- `PENDING`: Enqueued for dispatch.
- `ACCEPTED`: External provider accepted the HTTP API payload.
- `SENT`: Provider handed the message to telecom carrier / mail transfer agent.
- `DELIVERED`: Delivered to recipient handset / mailbox (confirmed via webhook).
- `READ`: Customer opened the message (WhatsApp blue tick / email tracking pixel).
- `FAILED`: Terminal delivery failure with reason code.
