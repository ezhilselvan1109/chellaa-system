# Roles and Permissions Matrix

Comprehensive RBAC matrix defining operational permissions across endpoints.

---

## 1. Role Inventory

- `ROLE_CUSTOMER`: End-consumer purchasing products and managing personal orders.
- `ROLE_ADMIN`: Super-administrator with complete platform governance authority.
- `ROLE_MERCHANDISER`: Catalog, brand, category, and campaign management.
- `ROLE_WAREHOUSE_OPERATOR`: Stock adjustments, picking, packing, and dispatch execution.
- `ROLE_CUSTOMER_SUPPORT`: Order timeline investigation, return review, customer assistance.
- `ROLE_FINANCE`: Tax reports, financial reconciliation, refund audits.

---

## 2. Permissions Matrix

| Resource / Endpoint | `CUSTOMER` | `MERCHANDISER` | `OPERATOR` | `SUPPORT` | `FINANCE` | `ADMIN` |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Browse Catalog / Search | READ | READ | READ | READ | READ | FULL |
| Create / Edit Products | - | FULL | - | - | - | FULL |
| Place Order / Checkout | OWN | - | - | - | - | FULL |
| View Customer Orders | OWN | - | READ | READ | READ | FULL |
| Cancel Order (Pre-Dispatch)| OWN | - | - | WRITE | - | FULL |
| Warehouse Stock Adjustment| - | - | WRITE | - | - | FULL |
| Carrier Dispatch / AWB | - | - | WRITE | - | - | FULL |
| Approve Return Claim | - | - | - | WRITE | - | FULL |
| Quality Check Inspection | - | - | WRITE | - | - | FULL |
| Trigger Payout Refund | - | - | - | WRITE | WRITE | FULL |
| Tax / Sales Reports | - | - | - | - | FULL | FULL |
| User Role Governance | - | - | - | - | - | FULL |
