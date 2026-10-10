# Business Model Rules

## 1. Single-Owner Merchant Tenet

1. **Sole Commercial Ownership**: The platform is owned, operated, and legally represented by exactly one merchant enterprise. All products sold, revenues collected, brand relationships maintained, and warranties issued belong to this single entity.
2. **Absolute Marketplace Prohibition**:
   - The platform strictly forbids multi-vendor or marketplace mechanisms.
   - Prohibited entities: Third-party sellers, vendor accounts, seller onboarding portals, seller commission tables, vendor settlement engines, seller-specific ratings, and multi-vendor order routing.
   - Any pull request or architecture proposal introducing vendor-specific abstractions shall be automatically rejected as a non-negotiable architectural violation.
3. **Internal Fulfillment & Warehouses**:
   - The owner may operate one or multiple physical fulfillment centers or warehouses.
   - All warehouses are company-owned and company-operated fulfillment nodes.
   - Inventory stored in any warehouse is the property of the single business owner.

## 2. Customer & Administrative Separation

1. **Customer Sphere**: Retail consumers interact exclusively with the public customer-facing storefront (`customer-web`) and mobile clients. Customers have no visibility into internal warehouse bins, wholesale procurement costs, supplier identities, or operational dispatch queues.
2. **Administrative Sphere**: Internal employees and authorized operations staff interact exclusively with the internal operations portal (`admin-web`).
3. **Role-Based Governance**: Access within the operations portal is strictly governed by least-privilege Role-Based Access Control (RBAC):
   - `ROLE_ADMIN`: Full commercial, financial, and configuration authority.
   - `ROLE_MERCHANDISER`: Catalog, category, brand, and campaign creation.
   - `ROLE_WAREHOUSE_OPERATOR`: Stock adjustments, picking, packing, and dispatch execution.
   - `ROLE_CUSTOMER_SUPPORT`: Order investigation, return review, customer assistance.
   - `ROLE_FINANCE`: Financial reports, tax summaries, refund audits.
