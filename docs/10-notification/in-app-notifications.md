# In-App Notification Channel Specification

Persistent customer notification inbox, read/unread states, and badge counters.

---

## 1. Customer Notification Inbox

- Stored in PostgreSQL table `in_app_notifications` with columns: `notification_id`, `customer_id`, `title`, `body`, `action_url`, `is_read`, `created_at`.
- Endpoints:
  - `GET /api/v1/notifications`: Paginated feed of customer notifications.
  - `GET /api/v1/notifications/unread-count`: Fast badge count query.
  - `PATCH /api/v1/notifications/{id}/read`: Mark notification as read.
  - `POST /api/v1/notifications/mark-all-read`: Bulk update unread items.
- Retention: In-app notifications automatically purge after 60 days.
