# Business Requirements Document (BRD)
## Document 05: Central Register of Business Rules

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-05 |
| **Document Title** | Enterprise Business Rules and Policy Catalog |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.1.0-REMEDIATED |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Governance Principles for Business Rules

This document establishes the central, definitive register of commercial rules, constraints, and validation logic governing the Chellaa E-Commerce Platform.
1. **Business-Driven Enforcement:** Business rules reflect real-world commercial obligations, inventory protection, legal statutes, and consumer protection policies.
2. **Deterministic & Testable:** Every rule specifies an unambiguous condition, expected outcome, and strict failure handling.
3. **No Unapproved Assumptions:** Where an operational policy is not yet confirmed by the business owner, it is explicitly classified as `PROPOSED` or `UNDECIDED` and mapped to the Open Questions register in [08-assumptions-dependencies-open-questions.md](./08-assumptions-dependencies-open-questions.md).

---

## 2. Business Rules Catalog by Operational Domain

```mermaid
graph TD
    subgraph Merchandising
        CAT[Catalog & Variant Governance]
        BRD[Brand Integrity]
        CTG[Category Schemas]
        PRC[Pricing & Margins]
        SRCH[Search & Facets]
        PDP[Product Browsing]
    end
    subgraph Commerce Core
        ADM[Administration & RBAC]
        CUST[Customer Accounts]
        INV[Stock Integrity & Holds]
        CART[Cart Constraints]
        CHK[Checkout Validation]
        ORD[Order Lifecycle]
        PAY[Payments & Gateways]
    end
    subgraph Post-Order & Ops
        SHP[Shipping & Logistics]
        RET[Returns & Refunds]
        FIN[Taxes & Financial Docs]
        NTF[Notifications & Support]
        SEC[Security & Privacy]
        AUD[Auditing & Continuity]
    end
```

---

### 2.1 Administration & Role Governance

#### BR-ADM-001: Separation of Duties (Finance vs Fulfillment)
* **Rule Statement:** An internal staff account cannot hold both "Finance Specialist" (refund and credit note authorization) and "Fulfillment Specialist" (warehouse dispatch and order packing) roles simultaneously.
* **Business Rationale:** Internal fraud prevention control; prevents a single operator from creating fictitious orders, dispatching goods, and issuing refunds to self-controlled accounts.
* **Applicable Domain:** Administration & RBAC
* **Trigger / Condition:** Administrator assigns roles to a staff account.
* **Expected Outcome:** If role combination violates separation of duties, the system blocks role assignment.
* **Exceptions:** Executive Business Owner account holding super-admin override privileges.
* **Related Requirements:** [FR-ADM-001](./04-functional-requirements.md#fr-adm-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner / System Administrator.

#### BR-ADM-002: Immediate Session Invalidation on Account Suspension
* **Rule Statement:** When an administrator marks a staff or customer account as `SUSPENDED` or `DEACTIVATED`, all active user sessions, refresh tokens, and API authorizations for that user must be immediately terminated within $\le 1\text{ second}$.
* **Business Rationale:** Security control to immediately revoke platform access for terminated employees or compromised customer accounts.
* **Applicable Domain:** Administration & Security
* **Trigger / Condition:** Account status transitions to `SUSPENDED` or `DEACTIVATED`.
* **Expected Outcome:** System publishes an account revocation event, blacklists active tokens, and rejects any in-flight API calls with HTTP 401.
* **Exceptions:** None.
* **Related Requirements:** [FR-ADM-001](./04-functional-requirements.md#fr-adm-001), [FR-ADM-002](./04-functional-requirements.md#fr-adm-002).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Security Lead.

---

### 2.2 Customer Account & Identity Governance

#### BR-CUST-001: Enterprise-Wide Uniqueness of Customer Mobile and Email
* **Rule Statement:** A mobile phone number and email address must be strictly unique across all active registered customer accounts. No two accounts can share the same verified mobile number.
* **Business Rationale:** Establishes unique customer identity for legal invoicing, OTP verification, and prevents multiple account abuse for promotional coupons.
* **Applicable Domain:** Customer Accounts
* **Trigger / Condition:** Customer registration or profile update.
* **Expected Outcome:** If the mobile number or email already exists in an active account, the action is blocked with an error prompt.
* **Exceptions:** None.
* **Related Requirements:** [FR-CUST-001](./04-functional-requirements.md#fr-cust-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead.

#### BR-CUST-002: Delivery Address Immutability for In-Transit Consignments
* **Rule Statement:** A customer or support representative cannot edit or modify the delivery address on an order once the order has transitioned to `PACKED` or `DISPATCHED` status.
* **Business Rationale:** Courier shipping labels and Air Waybills (AWBs) are booked against specific PIN codes. Modifying addresses in transit causes logistics rerouting failures and parcel misplacement.
* **Applicable Domain:** Customer Accounts & Orders
* **Trigger / Condition:** Address edit attempt on an existing order.
* **Expected Outcome:** If order status $\ge \text{PACKED}$, address editing is blocked.
* **Exceptions:** None. Customer must refuse delivery or coordinate with courier directly.
* **Related Requirements:** [FR-CUST-003](./04-functional-requirements.md#fr-cust-003), [FR-ORD-001](./04-functional-requirements.md#fr-ord-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead.

---

### 2.3 Catalog, Brand & Category Governance

#### BR-CAT-001: Global SKU and Barcode Uniqueness
* **Rule Statement:** Every sellable product variant must possess an enterprise-wide unique Stock Keeping Unit (SKU) code and an internationally valid unique barcode (EAN-13, UPC-A, or equivalent).
* **Business Rationale:** Ensures unambiguous identification across inventory receiving, warehouse bin picking, order fulfillment, and barcode scanning. Prevents cross-contamination of physical stock.
* **Applicable Domain:** Product Catalog & Inventory
* **Trigger / Condition:** Creation or update of a product variant.
* **Expected Outcome:** The system validates that neither the SKU nor barcode exists on any other active or archived variant. If duplicate, operation is rejected.
* **Exceptions:** None.
* **Related Requirements:** [FR-CAT-001](./04-functional-requirements.md#fr-cat-001), [FR-INV-001](./04-functional-requirements.md#fr-inv-001).
* **Severity / Strictness:** Critical (Hard Block).
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner / Catalog Lead.

#### BR-CAT-002: Product Publication Preconditions
* **Rule Statement:** A product cannot transition to `PUBLISHED` status unless it is linked to an active Brand, an active Leaf Category, has at least one active Variant with valid pricing ($\text{Selling Price} \le \text{MRP}$), has at least one high-resolution primary image, and fulfills all mandatory category specifications.
* **Business Rationale:** Protects storefront aesthetic quality and ensures customers have complete information before purchasing.
* **Applicable Domain:** Catalog Merchandising
* **Trigger / Condition:** Operator requests product publication.
* **Expected Outcome:** System executes pre-publication validation checklist. If any item is incomplete, status remains `DRAFT` and missing items are flagged.
* **Exceptions:** None.
* **Related Requirements:** [FR-CAT-001](./04-functional-requirements.md#fr-cat-001), [FR-CAT-002](./04-functional-requirements.md#fr-cat-002).
* **Severity / Strictness:** Critical (Hard Block).
* **Status:** `CONFIRMED`
* **Decision Owner:** Catalog Lead.

#### BR-CAT-003: Mandatory High-Resolution Primary Image for Published Variants
* **Rule Statement:** Every published product variant must have at least one valid, high-resolution primary thumbnail image (minimum resolution $1000 \times 1000\text{ pixels}$, maximum file size $5\text{MB}$).
* **Business Rationale:** E-commerce consumer electronics purchasing depends heavily on clear visual representation. Prevents broken image placeholders on storefronts.
* **Applicable Domain:** Catalog Merchandising & Media
* **Trigger / Condition:** Variant publication.
* **Expected Outcome:** If no primary image is uploaded and mapped, publication of the variant is blocked.
* **Exceptions:** None.
* **Related Requirements:** [FR-CAT-003](./04-functional-requirements.md#fr-cat-003).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Merchandising Lead.

#### BR-BRD-001: Brand Deletion and Archival Constraint
* **Rule Statement:** A brand cannot be deleted or deactivated if there are active products currently associated with it.
* **Business Rationale:** Prevents orphaned catalog items and broken customer search filters.
* **Applicable Domain:** Brand Management
* **Trigger / Condition:** Operator requests brand deletion or deactivation.
* **Expected Outcome:** System checks for active products linked to the brand ID. If found, action is blocked with an explanatory error.
* **Exceptions:** Products must be reassigned to another brand or archived first.
* **Related Requirements:** [FR-BRD-001](./04-functional-requirements.md#fr-brd-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Catalog Lead.

#### BR-CTG-001: Circular Category Reference Prevention
* **Rule Statement:** Category hierarchy must maintain a strict directed acyclic tree structure; a category cannot be assigned as a parent or child of itself or any of its descendants.
* **Business Rationale:** Prevents infinite navigation loops on storefront menus and search aggregation failures.
* **Applicable Domain:** Category Management
* **Trigger / Condition:** Category parent reassignment.
* **Expected Outcome:** System validates proposed category tree for cycles; rejects update if a cycle is detected.
* **Exceptions:** None.
* **Related Requirements:** [FR-CTG-001](./04-functional-requirements.md#fr-ctg-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Catalog Lead.

#### BR-CTG-002: Mandatory Specification Schema Enforcement
* **Rule Statement:** A product assigned to a category must provide validated values for all technical attributes designated as "Mandatory" in that category's schema.
* **Business Rationale:** Enables structured customer faceted search, side-by-side product comparisons, and complete technical transparency.
* **Applicable Domain:** Category & Catalog Management
* **Trigger / Condition:** Saving a product assigned to a category.
* **Expected Outcome:** Validation passes only when all required attributes have valid values adhering to data types.
* **Exceptions:** None.
* **Related Requirements:** [FR-CAT-002](./04-functional-requirements.md#fr-cat-002).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Merchandising Lead.

---

### 2.4 Pricing, Margins & Discounts

#### BR-PRC-001: Maximum Retail Price (MRP) Cap
* **Rule Statement:** The Selling Price (effective offer price) of any product variant must be less than or equal to its Maximum Retail Price ($\text{Selling Price} \le \text{MRP}$) at all times.
* **Business Rationale:** Statutory compliance with retail consumer protection laws in India (Legal Metrology Act) and truth-in-advertising guidelines.
* **Applicable Domain:** Pricing Management
* **Trigger / Condition:** Setting or updating variant pricing.
* **Expected Outcome:** If an operator attempts to input a Selling Price greater than MRP, the update is blocked with a validation error.
* **Exceptions:** None.
* **Related Requirements:** [FR-PRC-001](./04-functional-requirements.md#fr-prc-001), [FR-CAT-001](./04-functional-requirements.md#fr-cat-001).
* **Severity / Strictness:** Critical (Statutory Obligation).
* **Status:** `PROPOSED` (Subject to [OQ-009](./08-assumptions-dependencies-open-questions.md#oq-009))
* **Decision Owner:** Enterprise Business Owner / Legal.

#### BR-PRC-002: Minimum Margin Floor Alert
* **Rule Statement:** If an administrative price reduction causes the gross margin percentage to drop below a configured threshold (e.g., $8\%$), the price change cannot take effect without explicit secondary approval from the Finance Officer or Business Owner.
* **Business Rationale:** Prevents catastrophic revenue loss due to accidental data entry errors.
* **Applicable Domain:** Pricing Management
* **Trigger / Condition:** Catalog price reduction request.
* **Expected Outcome:** If calculated margin $< \text{Threshold}$, the change is routed to the `PENDING_APPROVAL` pricing queue.
* **Exceptions:** Executive Business Owner override with mandatory reason code.
* **Related Requirements:** [FR-PRC-001](./04-functional-requirements.md#fr-prc-001), [FR-AUD-001](./04-functional-requirements.md#fr-aud-001).
* **Severity / Strictness:** High.
* **Status:** `PROPOSED`
* **Decision Owner:** Finance Lead / Business Owner.

---

### 2.5 Inventory Integrity, Reservations & Warehousing

#### BR-INV-001: Non-Negative Available Inventory Enforcement
* **Rule Statement:** Available-to-Sell (ATS) inventory can never become negative under any circumstances. Physical On-Hand stock cannot drop below zero.
* **Business Rationale:** Backorders and pre-orders are excluded from baseline launch scope. Zero inventory strictly halts order placement to prevent unfulfillable customer promises.
* **Applicable Domain:** Inventory Management
* **Trigger / Condition:** Order checkout, stock deduction, or inventory adjustment.
* **Expected Outcome:** If an operation would cause $\text{ATS} < 0$, the operation is immediately blocked.
* **Exceptions:** None for baseline launch.
* **Related Requirements:** [FR-INV-001](./04-functional-requirements.md#fr-inv-001).
* **Severity / Strictness:** Critical (Core Commercial Invariant).
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner.

#### BR-INV-002: Available-to-Sell (ATS) Formula
* **Rule Statement:** The Available-to-Sell stock for any variant at any fulfillment location is strictly computed as:
$$\text{ATS} = \max(0, \text{On-Hand} - \text{Reserved} - \text{Safety Stock})$$
* **Business Rationale:** Ensures safety buffers (to absorb warehouse damage/theft) and active checkout reservations are strictly protected from duplicate sale.
* **Applicable Domain:** Inventory Management
* **Trigger / Condition:** Real-time stock queries and reservation attempts.
* **Expected Outcome:** The system computes ATS dynamically using the verified balance ledger.
* **Exceptions:** None.
* **Related Requirements:** [FR-INV-001](./04-functional-requirements.md#fr-inv-001), [FR-INV-002](./04-functional-requirements.md#fr-inv-002).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead.

#### BR-INV-003: Atomic Reservation and Reservation TTL Window
* **Rule Statement:** Inventory reservation occurs at the point of checkout initiation, holding stock for exactly 15 minutes ($\text{TTL} = 900\text{ seconds}$). If payment confirmation is not received within this window, the reservation expires automatically and stock is returned to ATS.
* **Business Rationale:** Protects customers during the active payment gateway interaction while preventing abandoned checkouts from permanently locking up high-demand electronics inventory.
* **Applicable Domain:** Inventory & Checkout
* **Trigger / Condition:** Customer navigates to payment submission.
* **Expected Outcome:** Atomic hold placed on SKU quantity. Scheduled expiration job releases holds if order remains unpaid at $T + 15\text{ mins}$.
* **Exceptions:** Configurable per-campaign TTL override by Business Owner.
* **Related Requirements:** [FR-INV-003](./04-functional-requirements.md#fr-inv-003), [FR-CHK-001](./04-functional-requirements.md#fr-chk-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead / Business Owner.

#### BR-INV-004: Inward Quality Segregation (Quarantine Put-Away)
* **Rule Statement:** Newly received inventory or returned customer goods cannot enter `Available-to-Sell` status until an authorized warehouse operator inspects the items and formally moves them from `QUARANTINE` or `RECEIVING` to `AVAILABLE`.
* **Business Rationale:** Prevents damaged, tampered, or opened-box electronics from being shipped to unsuspecting retail customers.
* **Applicable Domain:** Warehousing & Reverse Logistics
* **Trigger / Condition:** Physical receipt of inward shipments or return parcels.
* **Expected Outcome:** Goods are held in non-sellable staging locations until quality inspection sign-off.
* **Exceptions:** None.
* **Related Requirements:** [FR-INV-001](./04-functional-requirements.md#fr-inv-001), [FR-RET-001](./04-functional-requirements.md#fr-ret-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Warehouse Operations Lead.

---

### 2.6 Search, Browsing & Merchandising

#### BR-SRCH-001: Strict Exclusion of Draft and Inactive Items from Search Indices
* **Rule Statement:** Products and variants in `DRAFT`, `ARCHIVED`, `UNPUBLISHED`, or `DISCONTINUED` status must be strictly excluded from customer search results, category listings, and recommendation indices.
* **Business Rationale:** Prevents customers from finding or attempting to checkout unreleased or unapproved commercial items.
* **Applicable Domain:** Search & Discovery
* **Trigger / Condition:** Product search query execution or search index update.
* **Expected Outcome:** Search engine filters include only items where $\text{Product Status} = \text{PUBLISHED}$ and $\text{Variant Status} = \text{ACTIVE}$.
* **Exceptions:** Administrative catalog preview searches executed by authenticated staff.
* **Related Requirements:** [FR-SRCH-001](./04-functional-requirements.md#fr-srch-001), [FR-SRCH-002](./04-functional-requirements.md#fr-srch-002).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Merchandising Lead.

#### BR-SRCH-002: Out-of-Stock Search Display & Stockout Flagging
* **Rule Statement:** Products that are published but currently have $\text{ATS} = 0$ must remain discoverable in search and category browsing, but must be visually badged as "Out of Stock" and ranked below available items by default.
* **Business Rationale:** Allows customers to discover product specifications and register for back-in-stock alerts, while prioritizing available inventory to maximize conversion.
* **Applicable Domain:** Search & Discovery
* **Trigger / Condition:** Search result compilation.
* **Expected Outcome:** Out-of-stock items are flagged in result payloads; customers can filter out-of-stock items via a dedicated facet toggle.
* **Exceptions:** None.
* **Related Requirements:** [FR-SRCH-001](./04-functional-requirements.md#fr-srch-001), [FR-SRCH-002](./04-functional-requirements.md#fr-srch-002).
* **Severity / Strictness:** Medium.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead.

#### BR-PDP-001: Out-of-Stock Variant Add-to-Cart Action Disable
* **Rule Statement:** On the Product Details Page (PDP), when a customer selects a variant configuration that has $\text{ATS} = 0$, the "Add to Cart" and "Buy Now" buttons must be disabled and replaced with a "Notify Me When Available" action.
* **Business Rationale:** Prevents customers from adding unfulfillable items to cart, reducing checkout errors and user frustration.
* **Applicable Domain:** Product Experience
* **Trigger / Condition:** Customer switches variant selector on PDP.
* **Expected Outcome:** Storefront UI inspects variant ATS in real-time; disables purchasing controls if ATS $\le 0$.
* **Exceptions:** None.
* **Related Requirements:** [FR-PDP-001](./04-functional-requirements.md#fr-pdp-001), [FR-ADV-002](./04-functional-requirements.md#fr-adv-002).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead.

#### BR-WSH-001: Live Catalog Price Evaluation for Wishlist Saved Items
* **Rule Statement:** Wishlist records do not preserve historical price locks. When a customer views their wishlist, the items must display the current live catalog selling price, highlighting any price delta (price drops or increases) since the item was saved.
* **Business Rationale:** Prevents customer misunderstanding that adding an item to wishlist locks the price indefinitely.
* **Applicable Domain:** Wishlist Management
* **Trigger / Condition:** Customer accesses wishlist.
* **Expected Outcome:** System fetches live active variant pricing and computes difference against saved snapshot price.
* **Exceptions:** None.
* **Related Requirements:** [FR-WSH-001](./04-functional-requirements.md#fr-wsh-001).
* **Severity / Strictness:** Medium.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead.

---

### 2.7 Shopping Cart, Checkout & Quantity Limits

#### BR-CART-001: Cart Inventory Reservation Exemption
* **Rule Statement:** Adding an item to a shopping cart or wishlist does NOT reserve inventory. Inventory is reserved only upon initiating checkout.
* **Business Rationale:** Prevents customers from hoarding limited inventory by keeping items in abandoned carts indefinitely.
* **Applicable Domain:** Shopping Cart
* **Trigger / Condition:** Customer adds product to cart.
* **Expected Outcome:** Item is added to customer's cart list, but Available-to-Sell stock remains unaffected.
* **Exceptions:** None.
* **Related Requirements:** [FR-CART-001](./04-functional-requirements.md#fr-cart-001), [FR-INV-003](./04-functional-requirements.md#fr-inv-003).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead / Business Owner.

#### BR-CART-002: Maximum Purchase Quantity per SKU
* **Rule Statement:** The system enforces a configurable maximum purchase limit per SKU per customer per order (default: 5 units; restricted promotional SKUs: 1 or 2 units).
* **Business Rationale:** Prevents unauthorized commercial resellers from buying up retail consumer inventory for unauthorized secondary reselling.
* **Applicable Domain:** Shopping Cart & Checkout
* **Trigger / Condition:** Customer increments item quantity in cart.
* **Expected Outcome:** Quantity selector is capped at the maximum limit.
* **Exceptions:** Verified B2B bulk purchases authorized by Business Owner.
* **Related Requirements:** [FR-CART-002](./04-functional-requirements.md#fr-cart-002), [FR-CHK-001](./04-functional-requirements.md#fr-chk-001).
* **Severity / Strictness:** Medium.
* **Status:** `PROPOSED`
* **Decision Owner:** Commercial Lead.

#### BR-CHK-001: Mandatory Server-Side Total Recalculation
* **Rule Statement:** The final payable amount must be calculated entirely on the server immediately prior to payment gateway authorization. Any client-submitted prices, discounts, or tax totals are strictly ignored.
* **Business Rationale:** Prevents client-side price tampering or parameter manipulation cyber attacks.
* **Applicable Domain:** Checkout Security
* **Trigger / Condition:** Customer clicks "Proceed to Payment".
* **Expected Outcome:** Server evaluates current catalog price, re-runs coupon rules, computes exact delivery fee and statutory tax, and initiates payment intent strictly for the server-calculated sum.
* **Exceptions:** None.
* **Related Requirements:** [FR-CHK-001](./04-functional-requirements.md#fr-chk-001), [FR-CHK-002](./04-functional-requirements.md#fr-chk-002).
* **Severity / Strictness:** Critical (Zero Tolerance Security Rule).
* **Status:** `CONFIRMED`
* **Decision Owner:** System Architect / Security Lead.

#### BR-CHK-002: Cart Price Change Transparency & Re-approval
* **Rule Statement:** If the catalog price of an item changes while it resides in a customer’s cart, the checkout system must block order submission, update the cart to the new active price, and require explicit customer acknowledgment before allowing payment.
* **Business Rationale:** Transparent customer experience; prevents bait-and-switch accusations or unexpected billing charges.
* **Applicable Domain:** Shopping Cart & Checkout
* **Trigger / Condition:** Checkout validation detects price discrepancy between cart snapshot and live catalog.
* **Expected Outcome:** Checkout is interrupted; customer is presented with clear before/after price notice.
* **Exceptions:** None.
* **Related Requirements:** [FR-PRC-001](./04-functional-requirements.md#fr-prc-001), [FR-CHK-001](./04-functional-requirements.md#fr-chk-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead / Legal.

---

### 2.8 Order Management & Lifecycle

#### BR-ORD-001: Historical Order Snapshot Immutability
* **Rule Statement:** Once an order is placed, its line items, purchased SKUs, unit prices, applied discounts, and billing/shipping addresses are frozen as an immutable historical record. Subsequent changes to catalog prices or product descriptions do not alter historical orders.
* **Business Rationale:** Legal and accounting integrity; contracts of sale cannot be retroactively altered by catalog merchandising changes.
* **Applicable Domain:** Order Management
* **Trigger / Condition:** Order creation.
* **Expected Outcome:** System persists a complete data snapshot of item details, prices, and tax rates at the exact time of order placement.
* **Exceptions:** None.
* **Related Requirements:** [FR-ORD-001](./04-functional-requirements.md#fr-ord-001), [FR-ORD-003](./04-functional-requirements.md#fr-ord-003).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Legal / Finance Lead.

#### BR-ORD-002: Customer Cancellation Cut-off Window
* **Rule Statement:** A customer may self-cancel an order through self-service only while the order is in `CONFIRMED` or `PROCESSING` status. Once the order transitions to `PACKED` or `DISPATCHED`, customer self-service cancellation is blocked.
* **Business Rationale:** Once an order is packed, labeled, and staged on courier pallets, intercepting the physical box creates immense operational cost and logistics friction. Dispatched orders must follow the standard return process.
* **Applicable Domain:** Order Management
* **Trigger / Condition:** Customer attempts to cancel order in order history.
* **Expected Outcome:** If status $\in \{\text{CONFIRMED}, \text{PROCESSING}\}$, cancellation succeeds, inventory is released, and full refund is triggered. If $\ge \text{PACKED}$, cancellation is denied with instruction to refuse delivery or initiate a return.
* **Exceptions:** Administrative override by Customer Support Supervisor prior to vehicle departure.
* **Related Requirements:** [FR-ORD-001](./04-functional-requirements.md#fr-ord-001), [FR-ORD-002](./04-functional-requirements.md#fr-ord-002).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead.

#### BR-ORD-003: Order Confirmation Definition (Payment + Inventory)
* **Rule Statement:** An order is legally and operationally considered `CONFIRMED` only when both of the following conditions are simultaneously met:
  1. Payment is successfully captured (or COD authorized);
  2. Inventory reservation is successfully committed to the order.
* **Business Rationale:** Prevents issuing order confirmations for uncollectible transactions or unfulfillable inventory.
* **Applicable Domain:** Order Management & Payments
* **Trigger / Condition:** Ingestion of payment success event.
* **Expected Outcome:** Order status transitions to `CONFIRMED`, tax invoice is generated, and customer confirmation notification is dispatched.
* **Exceptions:** None.
* **Related Requirements:** [FR-ORD-001](./04-functional-requirements.md#fr-ord-001), [FR-PAY-001](./04-functional-requirements.md#fr-pay-001), [FR-INV-003](./04-functional-requirements.md#fr-inv-003).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner.

#### BR-ORD-004: In-Flight Delivery Address Modification Cutoff
* **Rule Statement:** Delivery addresses can only be modified while an order is in `CONFIRMED` status and prior to picking list generation. Once status is `PROCESSING`, address modifications are strictly blocked.
* **Business Rationale:** Warehouse picklists and packing systems bind order items to specific destination routes.
* **Applicable Domain:** Order Management
* **Trigger / Condition:** Address change request received from customer or support.
* **Expected Outcome:** If status $\ge \text{PROCESSING}$, change is rejected.
* **Exceptions:** None.
* **Related Requirements:** [FR-ORD-001](./04-functional-requirements.md#fr-ord-001), [FR-CUST-003](./04-functional-requirements.md#fr-cust-003).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead.

---

### 2.9 Payments, Gateways & Reconciliation

#### BR-PAY-001: Webhook Idempotency and Duplicate Event Handling
* **Rule Statement:** Payment gateway webhook events must be processed idempotently. Processing a duplicate webhook event for an already confirmed or refunded transaction must return HTTP 200 without executing duplicate business actions.
* **Business Rationale:** Payment gateways frequently retry webhooks over unreliable networks. Duplicate processing could trigger double orders or duplicate refunds.
* **Applicable Domain:** Payment Gateway Integration
* **Trigger / Condition:** Inbound webhook received from payment gateway.
* **Expected Outcome:** System checks event ID against payment ledger. If already processed, system logs duplicate event and returns immediate success response.
* **Exceptions:** None.
* **Related Requirements:** [FR-PAY-001](./04-functional-requirements.md#fr-pay-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Technical Lead.

#### BR-PAY-002: Late Payment Success on Expired Reservation Policy
* **Rule Statement:** If a payment gateway reports successful payment capture after the 15-minute checkout reservation has expired, the system must inspect current Available-to-Sell stock:
  * **Case A (Stock still available):** System immediately creates a new reservation, transitions order to `CONFIRMED`, and queues fulfillment.
  * **Case B (Stock exhausted/sold to another customer):** System transitions order to `PAYMENT_RECEIVED_OUT_OF_STOCK`, cancels the order, and automatically triggers a 100% full refund to the customer's source account within 1 hour.
* **Business Rationale:** Solves the critical edge case where slow banking networks confirm payment after stock has been released.
* **Applicable Domain:** Payment & Inventory Orchestration
* **Trigger / Condition:** Payment success event received on an expired checkout reservation.
* **Expected Outcome:** Automated resolution adhering to Case A or Case B; zero human intervention required.
* **Exceptions:** None.
* **Related Requirements:** [FR-PAY-001](./04-functional-requirements.md#fr-pay-001), [FR-PAY-002](./04-functional-requirements.md#fr-pay-002), [FR-INV-003](./04-functional-requirements.md#fr-inv-003), [FR-RET-003](./04-functional-requirements.md#fr-ret-003).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner / Operations Lead.

#### BR-PAY-003: Unknown Payment Status Active Reconciliation
* **Rule Statement:** If an order remains in `PENDING_PAYMENT` for $> 30\text{ minutes}$ without a conclusive webhook, the system must actively query the payment gateway verification API to fetch the authoritative transaction status before terminating the order.
* **Business Rationale:** Prevents cancelling orders where the customer actually paid but the gateway webhook was dropped or delayed.
* **Applicable Domain:** Payment Management
* **Trigger / Condition:** Scheduled background job identifying stale `PENDING_PAYMENT` orders.
* **Expected Outcome:** Active server-to-server inquiry. If gateway status is `SUCCESS`, order is confirmed; if `FAILED` or `NOT_FOUND`, order is cancelled and inventory released.
* **Exceptions:** None.
* **Related Requirements:** [FR-PAY-001](./04-functional-requirements.md#fr-pay-001), [FR-PAY-003](./04-functional-requirements.md#fr-pay-003).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead / Technical Lead.

---

### 2.10 Promotions, Coupons & Discounts

#### BR-PRM-001: Strict Coupon Non-Stacking Policy
* **Rule Statement:** Only one promotional coupon code may be applied per order. Promotional coupons cannot be combined or stacked with other coupon codes unless explicitly authorized by a campaign override rule.
* **Business Rationale:** Protects retailer margins against compounding discount abuse.
* **Applicable Domain:** Promotions Management
* **Trigger / Condition:** Customer attempts to apply a coupon code while another code is already active.
* **Expected Outcome:** System prompts user: "A coupon is already applied. Would you like to replace it with this new coupon?"
* **Exceptions:** Automatic shipping fee discounts (e.g., "Free Shipping on orders above ₹1,000") may stack with a product coupon.
* **Related Requirements:** [FR-PRM-001](./04-functional-requirements.md#fr-prm-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Marketing Lead / Business Owner.

#### BR-PRM-002: Pro-Rata Line-Item Discount Distribution
* **Rule Statement:** When an order-level discount is applied, the total discount must be allocated proportionally across all eligible line items based on their relative item values:
$$\text{Line Discount}_i = \text{Total Discount} \times \left( \frac{\text{Line Item Price}_i}{\sum \text{Eligible Items}} \right)$$
* **Business Rationale:** Essential for accurate line-item statutory tax calculation (GST) and fair partial refund calculation if only one item from an order is returned.
* **Applicable Domain:** Promotions & Accounting
* **Trigger / Condition:** Order total calculation with active promotion.
* **Expected Outcome:** Each line item stores its gross price, proportional discount, net taxable value, and tax amount.
* **Exceptions:** Item-specific coupons apply 100% of their discount to the targeted SKU only.
* **Related Requirements:** [FR-PRM-001](./04-functional-requirements.md#fr-prm-001), [FR-PRM-002](./04-functional-requirements.md#fr-prm-002), [FR-FIN-001](./04-functional-requirements.md#fr-fin-001), [FR-RET-003](./04-functional-requirements.md#fr-ret-003).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead / Accounting.

---

### 2.11 Shipping, Logistics & Delivery

#### BR-SHP-001: Mandatory Internal Shipment Identity and Auditable Dispatch Handover
* **Rule Statement:** An order cannot transition to `DISPATCHED` unless it is linked to a unique internal Shipment ID, completed packing/serial checks, and recorded dispatch assignment and handover evidence. For own-team delivery, record the assigned associate/run and custody acknowledgement. For a direct courier or aggregator lane, record the approved provider lane, booking/pickup reference and required handover evidence; an external AWB is mandatory only when that provider lane requires one. The internal Shipment ID remains canonical.
* **Business Rationale:** Ensures every dispatched parcel is traceable across own-team, direct-courier and aggregator delivery without making an external carrier identifier mandatory for every lane.
* **Applicable Domain:** Shipping & Logistics
* **Trigger / Condition:** A packed order is assigned and marked ready for dispatch or provider/associate handover.
* **Expected Outcome:** System validates shipment identity, packing/serial checks, lane-specific assignment and required custody/handover evidence. If any required record is absent, dispatch transition is blocked.
* **Exceptions:** An authorized supervisor may resolve a documented operational exception; all overrides require a reason and audit event. No silent bypass is permitted.
* **Related Requirements:** [FR-SHP-001](./04-functional-requirements.md#fr-shp-001).
* **Severity / Strictness:** High.
* **Status:** `PROPOSED` (provider-neutral policy drafted; operational owner review remains required).
* **Decision Owner:** Logistics Operations Lead.

#### BR-SHP-002: Provider-Neutral Failed-Delivery, Retry and Return Handling
* **Rule Statement:** Every failed delivery attempt must be recorded against the internal Shipment ID with source, timestamp and reason code. Own-team retry limits and schedules must follow the approved internal operating policy; direct-courier and aggregator retry/RTO behavior must be mapped from the approved provider contract into the common shipment lifecycle. After the applicable retry/return threshold, the system must create and track the return-to-warehouse or provider RTO path and preserve custody/return evidence.
* **Business Rationale:** Provides consistent exception visibility across delivery channels while preserving provider-specific rules and avoiding false assumptions that every lane follows the same retry process.
* **Applicable Domain:** Shipping & Delivery
* **Trigger / Condition:** Own-team staff or a validated provider event records a failed delivery attempt, reschedule, or return/RTO milestone.
* **Expected Outcome:** Customer notifications, retry eligibility, escalation, and return handling follow the approved lane-specific policy. Attempt history is append-only; authorized exceptions require a reason and audit event.
* **Exceptions:** Provider outage, invalid/unmapped event, or conflicting status must be quarantined for reconciliation rather than silently forcing a shipment state.
* **Related Requirements:** [FR-SHP-001](./04-functional-requirements.md#fr-shp-001), [FR-SHP-002](./04-functional-requirements.md#fr-shp-002).
* **Severity / Strictness:** Medium.
* **Status:** `PROPOSED` (provider-neutral policy drafted; retry limits and provider-specific rules require Operations review).
* **Decision Owner:** Logistics Operations Lead.

---

### 2.12 Returns, Replacements & Refunds

#### BR-RET-001: Strict Electronics Return Window Policy
* **Rule Statement:** A return or replacement request for consumer electronics must be initiated within exactly 7 calendar days of verified carrier delivery ($T_{\text{delivered}} + 7\text{ days}$). After this window, the self-service return option is permanently closed, and the customer must seek manufacturer brand warranty support.
* **Business Rationale:** Standard electronics retail policy; limits enterprise exposure to customer-induced device wear, accidental drops, or software tampering.
* **Applicable Domain:** Returns & Customer Support
* **Trigger / Condition:** Customer attempts to submit return request.
* **Expected Outcome:** If current time $> T_{\text{delivered}} + 7\text{ days}$, the system disables return submission and displays manufacturer authorized service center contact details.
* **Exceptions:** Executive Customer Support override authorized for documented transit defects or courier misdelivery.
* **Related Requirements:** [FR-RET-001](./04-functional-requirements.md#fr-ret-001).
* **Severity / Strictness:** High.
* **Status:** `PROPOSED` (Subject to [OQ-008](./08-assumptions-dependencies-open-questions.md#oq-008))
* **Decision Owner:** Enterprise Business Owner / Operations Lead.

#### BR-RET-002: Return Quality Grading & Resale Restocking Rule
* **Rule Statement:** A returned electronics item can only be returned to `Available-to-Sell` stock if:
  1. The box seal is verified intact/unopened; OR
  2. The item passes certified diagnostic inspection, is factory reset, repackaged, and approved by a Warehouse Supervisor. Opened or defective electronics must be moved to `REFURBISHED` or `DEFECTIVE_RTV` (Return to Vendor) inventory.
* **Business Rationale:** Selling opened, scratched, or previously activated electronics as brand-new violates consumer trade laws and destroys brand reputation.
* **Applicable Domain:** Reverse Logistics & Inventory
* **Trigger / Condition:** Physical return parcel inspection at warehouse.
* **Expected Outcome:** Inspector inputs grading: `SEALED_RESTOCKABLE`, `OPEN_BOX_REFURBISHED`, or `DEFECTIVE`. Only `SEALED_RESTOCKABLE` increments primary new ATS stock.
* **Exceptions:** None.
* **Related Requirements:** [FR-RET-001](./04-functional-requirements.md#fr-ret-001), [FR-INV-001](./04-functional-requirements.md#fr-inv-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Operations Lead / Quality Assurance.

#### BR-RET-003: Source-Instrument Refund Remittance Rule
* **Rule Statement:** Refunds must be remitted strictly to the original payment instrument used for purchase (Card $\rightarrow$ Same Card; UPI $\rightarrow$ Same VPA). Cash refunds for online payments are strictly prohibited.
* **Business Rationale:** Compliance with anti-money laundering (AML) laws and payment gateway scheme operating regulations.
* **Applicable Domain:** Financial Compliance & Refunds
* **Trigger / Condition:** Refund execution.
* **Expected Outcome:** System directs gateway refund API to the original transaction ID.
* **Exceptions:** For Cash on Delivery (COD) orders, refunds are disbursed via direct bank transfer (NEFT/IMPS) or UPI to customer-verified accounts after identity verification.
* **Related Requirements:** [FR-RET-002](./04-functional-requirements.md#fr-ret-002), [FR-RET-003](./04-functional-requirements.md#fr-ret-003), [FR-FIN-003](./04-functional-requirements.md#fr-fin-003).
* **Severity / Strictness:** Critical (Statutory AML Compliance).
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead / Legal.

---

### 2.13 Financial Invoices, Taxes & Credit Notes

#### BR-FIN-001: Tax-Inclusive Retail Price Display
* **Rule Statement:** All prices displayed on customer-facing storefronts (product listings, details, cart) must be gross tax-inclusive retail prices. The invoice and checkout summary must break out the embedded tax components (CGST, SGST, IGST).
* **Business Rationale:** Compliance with Indian consumer protection standards; prevents checkout price shock where taxes are unexpectedly added at the final step.
* **Applicable Domain:** Merchandising & Taxation
* **Trigger / Condition:** Price display and order calculation.
* **Expected Outcome:** Total payable matches display price; invoice displays net taxable value + itemized tax.
* **Exceptions:** None.
* **Related Requirements:** [FR-FIN-001](./04-functional-requirements.md#fr-fin-001), [FR-PRC-001](./04-functional-requirements.md#fr-prc-001).
* **Severity / Strictness:** Critical.
* **Status:** `PROPOSED` (Subject to [OQ-009](./08-assumptions-dependencies-open-questions.md#oq-009))
* **Decision Owner:** Finance Lead / Legal.

#### BR-FIN-002: Monotonic Sequential Invoice Numbering
* **Rule Statement:** Tax invoice numbers must be strictly sequential, monotonically increasing, unique, and continuous with zero gaps or duplicates within each financial year (e.g., `INV/2026-27/00001`).
* **Business Rationale:** Strict statutory tax requirement under GST legislation. Missing or duplicate invoice numbers incur severe regulatory penalties.
* **Applicable Domain:** Financial Invoicing
* **Trigger / Condition:** Invoicing on payment confirmation.
* **Expected Outcome:** Sequence generator provides atomic next number; failed order generation does not burn or skip numbers.
* **Exceptions:** None.
* **Related Requirements:** [FR-FIN-002](./04-functional-requirements.md#fr-fin-002).
* **Severity / Strictness:** Critical (Statutory Obligation).
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead / Statutory Auditor.

#### BR-FIN-003: Invoice Immutability & Credit Note Mandate
* **Rule Statement:** Once a tax invoice is issued, it cannot be edited, overwritten, or deleted. Any modification, order cancellation, or product return must be accounted for by issuing an official, sequential Credit Note referencing the original invoice.
* **Business Rationale:** Preserves financial accounting auditability and satisfies statutory tax filing audits.
* **Applicable Domain:** Financial Accounting
* **Trigger / Condition:** Return approval, cancellation after invoice issuance, or financial adjustment.
* **Expected Outcome:** System issues linked Credit Note with unique number (e.g., `CN/2026-27/00001`), reversing the exact taxable amount and tax liabilities.
* **Exceptions:** None.
* **Related Requirements:** [FR-FIN-002](./04-functional-requirements.md#fr-fin-002), [FR-FIN-003](./04-functional-requirements.md#fr-fin-003), [FR-RET-003](./04-functional-requirements.md#fr-ret-003).
* **Severity / Strictness:** Critical (Statutory Obligation).
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead.

---

### 2.14 Notifications, Reviews & Customer Support

#### BR-NTF-001: Transactional Alert Precedence over Marketing Opt-Outs
* **Rule Statement:** Transactional notifications (account OTPs, order confirmation, payment receipts, shipment tracking updates, refund alerts) must be dispatched regardless of the customer's promotional marketing consent status.
* **Business Rationale:** Transactional communications represent essential contract fulfillment notifications required by law and customer service standards.
* **Applicable Domain:** Notifications & Communications
* **Trigger / Condition:** Triggering of a transactional domain event.
* **Expected Outcome:** System routes transactional alerts to verified contact channels bypassing promotional suppression filters.
* **Exceptions:** None.
* **Related Requirements:** [FR-NTF-001](./04-functional-requirements.md#fr-ntf-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Product Lead / Legal.

#### BR-REV-001: Verified-Buyer Gate for Customer Ratings and Reviews
* **Rule Statement:** A customer may submit a product rating (1-5 stars) and written review only if the customer possesses an authenticated account with a completed, verified delivered order containing that specific SKU.
* **Business Rationale:** Prevents fake reviews, competitor sabotage, and spam ratings, ensuring high review authenticity.
* **Applicable Domain:** Ratings & Reviews
* **Trigger / Condition:** Customer attempts to submit a review.
* **Expected Outcome:** System verifies order history for delivered status of that SKU. If verified, review submission is accepted into moderation queue; if not, action is blocked.
* **Exceptions:** None.
* **Related Requirements:** [FR-REV-001](./04-functional-requirements.md#fr-rev-001).
* **Severity / Strictness:** Medium.
* **Status:** `PROPOSED` (Subject to [OQ-012](./08-assumptions-dependencies-open-questions.md#oq-012))
* **Decision Owner:** Product Lead.

#### BR-SUP-001: Support Ticket Response SLA (2 Hours for Critical Complaints)
* **Rule Statement:** Support inquiries classified as "Transit Damage", "Missing Shipment", or "Defective on Arrival (DOA)" must receive an initial agent response or automated courier investigation initiation within $\le 2\text{ business hours}$.
* **Business Rationale:** High customer anxiety on electronics delivery failures requires urgent operational intervention to prevent chargebacks and public negative reviews.
* **Applicable Domain:** Customer Support
* **Trigger / Condition:** Ingestion of a critical support ticket.
* **Expected Outcome:** System tags ticket priority as `CRITICAL`, triggers agent notification, and starts SLA escalation timer.
* **Exceptions:** Inquiries submitted outside business operating hours queue for next-business-day 09:00 AM dispatch.
* **Related Requirements:** [FR-SUP-001](./04-functional-requirements.md#fr-sup-001).
* **Severity / Strictness:** Medium.
* **Status:** `CONFIRMED`
* **Decision Owner:** Customer Care Lead.

#### BR-RPT-001: Sales Report Reconciliation with Invoiced and Confirmed Ledgers
* **Rule Statement:** All executive commercial reports (GMV, Gross Revenue, Net Revenue) must strictly reconcile with the sum of confirmed payment transactions and generated tax invoices, accounting for authorized credit notes.
* **Business Rationale:** Prevents executive reporting discrepancies between merchandising dashboards and official audited corporate financial statements.
* **Applicable Domain:** Analytics & Financial Reporting
* **Trigger / Condition:** Reporting aggregation job execution.
* **Expected Outcome:** Discrepancies between reporting projections and financial ledgers trigger automated alert to Finance Officer.
* **Exceptions:** None.
* **Related Requirements:** [FR-RPT-001](./04-functional-requirements.md#fr-rpt-001), [FR-FIN-002](./04-functional-requirements.md#fr-fin-002).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Finance Lead / Business Owner.

---

### 2.15 Security, Continuity & Advanced Merchandising

#### BR-SEC-001: Zero Raw Cardholder Data Storage (PCI-DSS Tokenization)
* **Rule Statement:** The platform must never store, transmit, or process raw Primary Account Numbers (PAN), CVVs, or card PINs within enterprise databases, application memories, or log files.
* **Business Rationale:** Mandatory compliance with PCI-DSS guidelines. Eliminates enterprise liability for payment card data breaches.
* **Applicable Domain:** Platform Security
* **Trigger / Condition:** All payment interactions.
* **Expected Outcome:** Platform utilizes hosted checkout fields / SDK tokenization provided by certified payment gateways.
* **Exceptions:** None.
* **Related Requirements:** [FR-SEC-001](./04-functional-requirements.md#fr-sec-001), [FR-PAY-001](./04-functional-requirements.md#fr-pay-001).
* **Severity / Strictness:** Critical (Zero Tolerance).
* **Status:** `CONFIRMED`
* **Decision Owner:** Chief Information Security Officer / Technical Lead.

#### BR-SEC-002: Account Lockout on Credential Brute-Force
* **Rule Statement:** If an account experiences 5 consecutive failed login attempts within 15 minutes, the account is locked for 30 minutes, and a security notification is dispatched to the registered owner.
* **Business Rationale:** Prevents automated credential-stuffing and brute-force password cracking attacks.
* **Applicable Domain:** Account Security
* **Trigger / Condition:** 5th failed authentication attempt.
* **Expected Outcome:** Account status set to `TEMPORARILY_LOCKED`; subsequent attempts rejected with lockout timer notice.
* **Exceptions:** Self-service password reset via verified OTP unlocks account immediately.
* **Related Requirements:** [FR-SEC-002](./04-functional-requirements.md#fr-sec-002), [FR-CUST-002](./04-functional-requirements.md#fr-cust-002).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Security Lead.

#### BR-AUD-001: Immutable Audit Trail (WORM Principle)
* **Rule Statement:** Audit log records are Write-Once, Read-Many (WORM). No user, including platform super-administrators, possesses software or operational permission to modify, edit, truncate, or delete audit records.
* **Business Rationale:** Ensures forensic integrity during fraud investigations and regulatory compliance inquiries.
* **Applicable Domain:** Auditing & Compliance
* **Trigger / Condition:** Mutation event on business entities.
* **Expected Outcome:** Audit records are appended to an immutable datastore.
* **Exceptions:** None.
* **Related Requirements:** [FR-AUD-001](./04-functional-requirements.md#fr-aud-001), [FR-ADM-002](./04-functional-requirements.md#fr-adm-002).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Enterprise Business Owner / Legal.

#### BR-DAT-001: Statutory Financial Record Retention Mandate (8 Financial Years)
* **Rule Statement:** Invoices, credit notes, payment records, and tax summaries must be retained in persistent archive storage for a minimum of 8 financial years (statutory requirement under Indian tax and companies law), even if the associated customer account is closed or deleted.
* **Business Rationale:** Compliance with corporate tax audit mandates overrides customer "Right to Erasure" under data privacy statutes.
* **Applicable Domain:** Data Retention & Privacy
* **Trigger / Condition:** Customer account deletion request or periodic data purge.
* **Expected Outcome:** Customer PII is anonymized from marketing and profile tables, but historical financial transaction documents are preserved in locked archives.
* **Exceptions:** None.
* **Related Requirements:** [FR-CUST-004](./04-functional-requirements.md#fr-cust-004), [FR-FIN-002](./04-functional-requirements.md#fr-fin-002).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Legal / Statutory Auditor.

#### BR-INT-001: Third-Party Provider Failures Must Never Cause Silent Data Loss
* **Rule Statement:** Outbound requests to third-party providers (courier APIs, payment gateways, messaging gateways) that fail due to network timeouts or 5xx HTTP errors must be persisted in an asynchronous dead-letter retry queue; silent transaction drops are strictly prohibited.
* **Business Rationale:** Prevents paid orders from being lost, parcels from remaining unpacked, or customer notifications from failing silently.
* **Applicable Domain:** External Integrations
* **Trigger / Condition:** Transient or fatal failure of external integration call.
* **Expected Outcome:** Operation payload is enqueued with exponential backoff retry metadata and alert notification.
* **Exceptions:** None.
* **Related Requirements:** [FR-INT-001](./04-functional-requirements.md#fr-int-001).
* **Severity / Strictness:** Critical.
* **Status:** `CONFIRMED`
* **Decision Owner:** Technical Lead.

#### BR-OPS-001: Mandatory Correlation Trace ID & PII Redaction in Structured Logs
* **Rule Statement:** All application logs must be structured JSON, must capture the propagated unique Correlation Identifier (Trace ID), and must automatically redact customer PII (credit cards, passwords, full mobile numbers, email addresses).
* **Business Rationale:** Enables rapid distributed troubleshooting while complying with DPDPA 2023 data privacy regulations.
* **Applicable Domain:** Platform Operations & SRE
* **Trigger / Condition:** Log emission from any service.
* **Expected Outcome:** Trace ID is present; sensitive customer strings are replaced with masked placeholders (e.g., `XXXXX`).
* **Exceptions:** None.
* **Related Requirements:** [FR-OPS-001](./04-functional-requirements.md#fr-ops-001), [NFR-OBS-001](./07-non-functional-requirements.md#nfr-obs-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** SRE / Security Lead.

#### BR-REL-001: Zero-Downtime Deployment & Automated Testing Quality Gate
* **Rule Statement:** Production software releases must be executed using zero-downtime rolling deployment strategies and must be blocked automatically if automated test suite execution (unit, contract, integration) achieves less than a 100% pass rate.
* **Business Rationale:** Prevents breaking in-flight checkout transactions and blocks regression bugs from reaching live customers.
* **Applicable Domain:** Release Management
* **Trigger / Condition:** Production release pipeline trigger.
* **Expected Outcome:** Automated quality gate verifies 100% test pass before initiating zero-downtime rolling container replacement.
* **Exceptions:** Emergency hotfix authorized in writing by Technical Lead and Business Owner.
* **Related Requirements:** [FR-REL-001](./04-functional-requirements.md#fr-rel-001), [NFR-REL-001](./07-non-functional-requirements.md#nfr-rel-001).
* **Severity / Strictness:** High.
* **Status:** `CONFIRMED`
* **Decision Owner:** Technical Lead.

#### BR-BCM-001: Disaster Recovery RTO and RPO Mandate
* **Rule Statement:** Transactional databases must maintain automated daily snapshots and continuous Write-Ahead Logging (WAL) ensuring Recovery Point Objective ($\text{RPO} \le 5\text{ minutes}$) and Recovery Time Objective ($\text{RTO} \le 2\text{ hours}$).
* **Business Rationale:** Guarantees business continuity and bounds financial transaction loss during catastrophic regional infrastructure failures.
* **Applicable Domain:** Business Continuity
* **Trigger / Condition:** Catastrophic cloud infrastructure failure.
* **Expected Outcome:** Disaster recovery runbook restores database state to within 5 minutes of incident within 2 hours.
* **Exceptions:** None.
* **Related Requirements:** [FR-BCM-001](./04-functional-requirements.md#fr-bcm-001), [NFR-BCM-001](./07-non-functional-requirements.md#nfr-bcm-001).
* **Severity / Strictness:** Critical.
* **Status:** `PROPOSED` (Subject to [OQ-017](./08-assumptions-dependencies-open-questions.md#oq-017))
* **Decision Owner:** Enterprise Business Owner.

#### BR-ADV-001: Product Bundle Availability Governed by Lowest Component ATS
* **Rule Statement:** For bundled electronics kits (e.g., Laptop + Mouse + Sleeve), the Available-to-Sell stock of the bundle parent SKU is strictly equal to the minimum ATS across all required component SKUs:
$$\text{ATS}_{\text{bundle}} = \min(\text{ATS}_{\text{item}_1}, \text{ATS}_{\text{item}_2}, \dots, \text{ATS}_{\text{item}_n})$$
* **Business Rationale:** Prevents selling a bundle kit when any individual component is physically out of stock.
* **Applicable Domain:** Advanced Merchandising
* **Trigger / Condition:** Bundle stock calculation.
* **Expected Outcome:** Dynamic calculation based on live component inventory balances.
* **Exceptions:** None.
* **Related Requirements:** [FR-ADV-001](./04-functional-requirements.md#fr-adv-001).
* **Severity / Strictness:** High.
* **Status:** `PROPOSED`
* **Decision Owner:** Merchandising Lead.

#### BR-ADV-002: Back-in-Stock Notification Throttling
* **Rule Statement:** When an out-of-stock product variant is replenished, back-in-stock notification alerts must be dispatched in batches proportional to the newly inwarded Available-to-Sell stock (e.g., if 10 units are received, dispatch alerts to top 30 queue subscribers).
* **Business Rationale:** Prevents sending 1,000 notifications for 10 units of inward stock, causing immediate mass customer stockout disappointment.
* **Applicable Domain:** Customer Engagement
* **Trigger / Condition:** Inward stock receipt on an SKU with active subscriber queue.
* **Expected Outcome:** System throttles notification dispatch according to configurable batch ratio.
* **Exceptions:** None.
* **Related Requirements:** [FR-ADV-002](./04-functional-requirements.md#fr-adv-002).
* **Severity / Strictness:** Medium.
* **Status:** `PROPOSED`
* **Decision Owner:** Marketing Lead.

---

## 3. Comprehensive Business Rules Summary Table

| Rule ID | Rule Title | Applicable Domain | Severity | Status |
| :--- | :--- | :--- | :---: | :--- |
| **BR-ADM-001** | Separation of Duties (Finance vs Fulfillment) | Administration | Critical | `CONFIRMED` |
| **BR-ADM-002** | Immediate Session Invalidation on Account Suspension | Security & Admin | Critical | `CONFIRMED` |
| **BR-CUST-001**| Unique Customer Phone and Email Identity | Customer Accounts | Critical | `CONFIRMED` |
| **BR-CUST-002**| In-Transit Delivery Address Immutability | Order Logistics | High | `CONFIRMED` |
| **BR-CAT-001** | Global SKU & Barcode Uniqueness | Catalog & Inventory | Critical | `CONFIRMED` |
| **BR-CAT-002** | Product Publication Checklist Preconditions | Catalog Merchandising | Critical | `CONFIRMED` |
| **BR-CAT-003** | Mandatory Primary Image for Published Variants | Catalog Merchandising | High | `CONFIRMED` |
| **BR-BRD-001** | Brand Deletion & Active Product Linkage | Brand Management | High | `CONFIRMED` |
| **BR-CTG-001** | Circular Category Reference Prevention | Category Management | Critical | `CONFIRMED` |
| **BR-CTG-002** | Mandatory Category Specification Schemas | Category Specifications| High | `CONFIRMED` |
| **BR-PRC-001** | Maximum Retail Price (MRP) Cap | Pricing Management | Critical | `PROPOSED` (OQ-009) |
| **BR-PRC-002** | Minimum Margin Floor Alert | Pricing Governance | High | `PROPOSED` |
| **BR-INV-001** | Non-Negative Available Inventory Enforcement | Inventory Integrity | Critical | `CONFIRMED` |
| **BR-INV-002** | Available-to-Sell (ATS) Calculation Formula | Inventory Management | Critical | `CONFIRMED` |
| **BR-INV-003** | Atomic Checkout Reservation & 15-Minute TTL | Inventory Reservations| Critical | `CONFIRMED` |
| **BR-INV-004** | Inward Quality Segregation (Quarantine Put-Away) | Warehousing & Inward | High | `CONFIRMED` |
| **BR-SRCH-001**| Exclusion of Draft & Inactive Items from Search | Search & Discovery | Critical | `CONFIRMED` |
| **BR-SRCH-002**| Out-of-Stock Search Display & Flagging | Search & Discovery | Medium | `CONFIRMED` |
| **BR-PDP-001** | Out-of-Stock Variant Add-to-Cart Action Disable| Product Experience | High | `CONFIRMED` |
| **BR-WSH-001** | Live Catalog Price Evaluation for Wishlist Items | Wishlist Management | Medium | `CONFIRMED` |
| **BR-CART-001**| Cart Inventory Reservation Exemption | Shopping Cart | High | `CONFIRMED` |
| **BR-CART-002**| Maximum Purchase Quantity per SKU | Cart & Anti-Scalping | Medium | `PROPOSED` |
| **BR-CHK-001** | Mandatory Server-Side Total Recalculation | Checkout Security | Critical | `CONFIRMED` |
| **BR-CHK-002** | Cart Price Change Transparency & Re-approval | Checkout Experience | High | `CONFIRMED` |
| **BR-ORD-001** | Historical Order Snapshot Immutability | Order Governance | Critical | `CONFIRMED` |
| **BR-ORD-002** | Customer Cancellation Cut-off Window | Order Lifecycle | High | `CONFIRMED` |
| **BR-ORD-003** | Order Confirmation Definition (Payment + Inventory)| Order Confirmation | Critical | `CONFIRMED` |
| **BR-ORD-004** | In-Flight Address Modification Cutoff | Order Operations | High | `CONFIRMED` |
| **BR-PAY-001** | Webhook Idempotency & Duplicate Replay Defense | Payment Gateways | Critical | `CONFIRMED` |
| **BR-PAY-002** | Late Payment Success on Expired Reservation Policy| Payment Orchestration | Critical | `CONFIRMED` |
| **BR-PAY-003** | Unknown Payment Status Active Reconciliation | Financial Auditing | High | `CONFIRMED` |
| **BR-PRM-001** | Strict Coupon Non-Stacking Policy | Promotions | High | `CONFIRMED` |
| **BR-PRM-002** | Pro-Rata Line-Item Discount Distribution | Accounting & Tax | Critical | `CONFIRMED` |
| **BR-SHP-001** | Mandatory Internal Shipment ID and Custody Record for Dispatch | Shipping Fulfillment | High | `PROPOSED` |
| **BR-SHP-002** | Multi-Attempt In-House Delivery and Return-to-Warehouse Protocol | In-House Delivery | Medium | `PROPOSED` |
| **BR-RET-001** | Strict 7-Day Electronics Return Window | Returns Management | High | `PROPOSED` (OQ-008) |
| **BR-RET-002** | Return Quality Grading & Sealed Restocking Rule | Reverse Logistics | Critical | `CONFIRMED` |
| **BR-RET-003** | Source-Instrument Refund Remittance Rule | Financial Compliance | Critical | `CONFIRMED` |
| **BR-FIN-001** | Tax-Inclusive Retail Price Display | Customer Merchandising| Critical | `PROPOSED` (OQ-009) |
| **BR-FIN-002** | Monotonic Sequential Invoice Numbering | Statutory Invoicing | Critical | `CONFIRMED` |
| **BR-FIN-003** | Invoice Immutability & Credit Note Mandate | Statutory Invoicing | Critical | `CONFIRMED` |
| **BR-NTF-001** | Transactional Message Precedence over Opt-outs | Notifications | High | `CONFIRMED` |
| **BR-REV-001** | Verified-Buyer Gate for Customer Reviews | Reviews & Ratings | Medium | `PROPOSED` (OQ-012) |
| **BR-SUP-001** | Support Ticket Response SLA (2 Hours Critical) | Customer Support | Medium | `CONFIRMED` |
| **BR-RPT-001** | Reconciled Sales Reporting Constraint | Reporting & Analytics| High | `CONFIRMED` |
| **BR-SEC-001** | Zero Raw Cardholder Data Storage (PCI-DSS) | Security & Privacy | Critical | `CONFIRMED` |
| **BR-SEC-002** | Account Lockout on Credential Brute-Force | Platform Security | High | `CONFIRMED` |
| **BR-AUD-001** | Immutable Audit Trail (WORM Principle) | Regulatory Auditing | Critical | `CONFIRMED` |
| **BR-DAT-001** | Statutory Financial Record Retention (8 Years) | Legal Retention | Critical | `CONFIRMED` |
| **BR-INT-001** | Zero Silent Data Loss on Provider Outage | Integrations | Critical | `CONFIRMED` |
| **BR-OPS-001** | Mandatory Correlation Trace ID & PII Redaction | Platform Operations | High | `CONFIRMED` |
| **BR-REL-001** | Zero-Downtime Deployment & Automated Testing Gate| CI/CD Quality | High | `CONFIRMED` |
| **BR-BCM-001** | Disaster Recovery RTO and RPO Mandate | Business Continuity | Critical | `PROPOSED` (OQ-017) |
| **BR-ADV-001** | Bundle Availability Governed by Lowest ATS Item | Advanced Merch | High | `PROPOSED` |
| **BR-ADV-002** | Back-in-Stock Notification Throttling | Customer Engagement | Medium | `PROPOSED` |
