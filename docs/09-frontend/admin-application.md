# Admin Operations Application (`apps/admin-web`)

Operations portal screens, data tables, RBAC visibility, and merchandising workflows.

---

## 1. Feature Modules

```text
apps/admin-web/src/features/
├── dashboard/       (Real-time revenue, order velocity, inventory health)
├── catalog/         (Product CRUD, Variant matrix, Scheduled publisher)
├── inventory/       (Multi-warehouse stock counts, Stock adjustment log)
├── orders/          (Order triage, Manual hold, Pick-list export, AWB label)
├── returns/         (Return request review, Warehouse QC grading, Refund trigger)
├── campaigns/       (Flash sale scheduler, Coupon generator, Budget tracker)
├── notifications/   (Template editor, WhatsApp preview, Dispatch logs)
├── customers/       (Customer 360 search, Order timeline, Support notes)
└── settings/        (RBAC user management, System audit logs)
```

---

## 2. Authorization & UI Safeguards

- UI navigation links and action buttons are gated by the user's verified role (`ROLE_ADMIN`, `ROLE_MERCHANDISER`, `ROLE_OPERATOR`).
- Note: Hiding a UI button is an ergonomics enhancement; the backend microservices strictly enforce authorization via Spring Security.
