# Business Requirements Document (BRD)

## Document 02: Stakeholders and Business Actors

| Document Attribute            | Details                                                                |
| :---------------------------- | :--------------------------------------------------------------------- |
| **Document ID**         | BRD-DOC-02                                                             |
| **Document Title**      | Stakeholder Ecosystem and Business Actor Profiles                      |
| **Project Name**        | Chellaa E-Commerce Platform                                            |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model**      | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C)  |
| **Version**             | 1.0.0-DRAFT                                                            |
| **Lifecycle Status**    | `PENDING_APPROVAL`                                                   |
| **Author**              | Senior Business Analyst & E-Commerce Requirements Engineering Team     |

---

## 1. Stakeholder Classification Model

The Chellaa E-Commerce Platform interacts with a diverse group of stakeholders, categorized into three distinct operational classifications:

1. **Internal Business Stakeholders & Operators (Human):** Employees and contractors who administer the platform, manage physical inventory, handle customer inquiries, and oversee financial performance.
2. **Customer Personas (Human):** End-users who browse, evaluate, purchase, and receive products through digital channels.
3. **External Service Providers & Systems (External Systems & Organizations):** Third-party commercial partners that execute mission-critical external utilities such as card payments, physical parcel transit, and telecommunications messaging.

```mermaid
graph TD
    subgraph External Systems
        ACT011[ACT-011: Payment Gateway]
        ACT012[ACT-012: Courier Logistics Partner]
        ACT013[ACT-013: Messaging Communications Gateway]
    end
    subgraph Platform Core
        SYS((Chellaa Commerce Engine))
    end
    subgraph Internal Business Actors
        ACT001[ACT-001: Business Owner]
        ACT002[ACT-002: System Administrator]
        ACT003[ACT-003: Catalog Manager]
        ACT004[ACT-004: Warehouse Operator]
        ACT005[ACT-005: Fulfillment Specialist]
        ACT006[ACT-006: Customer Support Agent]
        ACT007[ACT-007: Finance Officer]
        ACT008[ACT-008: Marketing Manager]
        ACT014[ACT-014: Observability SRE]
    end
    subgraph Customer Personas
        ACT009[ACT-009: Registered Customer]
        ACT010[ACT-010: Guest Shopper]
    end

    Customer Personas -->|Interact via APIs| SYS
    Internal Business Actors -->|Manage & Govern| SYS
    SYS <--> External Systems
```

---

## 2. Detailed Business Actor Profiles

### 2.1 ACT-001: Enterprise Business Owner

* **Actor Type:** Human (Executive Leadership)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Ultimate legal, operational, and commercial owner of the enterprise. Sets commercial policies, pricing targets, return policy windows, and reviews high-level profit and loss statements.
* **Goals:** Maximize gross revenue, protect brand reputation, minimize operational leakage/theft, and maintain high customer trust.
* **Permitted Business Activities:**
  * View executive analytics dashboards (gross revenue, net margins, top-selling categories, return rates).
  * Authorize non-standard commercial overrides (e.g., exceptional customer refunds, bulk write-offs).
  * Approve scope modifications, marketing budgets, and commercial policy updates.
  * Hold super-administrator privileges for emergency business interventions.
* **Information Accessed:** Executive financial summaries, profit margins, complete customer records, inventory valuations, operational logs.
* **Dependencies:** Accurate reporting data, audit logs, and verified financial reconciliation ledgers.
* **Related Feature IDs:** `FEAT-ADM-001`, `FEAT-RPT-001`, `FEAT-AUD-001`, `FEAT-FIN-001`.

---

### 2.2 ACT-002: Platform System Administrator

* **Actor Type:** Human (Technical Operations)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Manages business staff accounts, assigns administrative roles, monitors system operational parameters, and ensures business continuity.
* **Goals:** Maintain platform operational stability, prevent unauthorized access, and ensure security compliance.
* **Permitted Business Activities:**
  * Create, update, suspend, or terminate internal operator accounts.
  * Assign and revoke Role-Based Access Control (RBAC) permissions.
  * Configure system-wide operational parameters (session timeouts, lockouts, feature toggles).
  * Audit administrative access logs for anomalous behavior.
* **Information Accessed:** User access logs, role assignment tables, system configuration settings, security incident reports.
* **Dependencies:** Robust identity and access management controls.
* **Related Feature IDs:** `FEAT-ADM-001`, `FEAT-ADM-002`, `FEAT-SEC-001`, `FEAT-OPS-001`.

---

### 2.3 ACT-003: Catalog Manager

* **Actor Type:** Human (Merchandising Staff)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Maintains the integrity, hierarchy, and presentation of the electronics product catalog.
* **Goals:** Ensure products are accurately classified, variants are correctly mapped, specifications are detailed, and assets are high quality.
* **Permitted Business Activities:**
  * Create, edit, publish, unpublish, and archive products and variants.
  * Define category hierarchies (parent-child categories) and attribute schemas (RAM, storage, display size).
  * Manage brand records (logos, manufacturer info, warranty terms).
  * Execute bulk catalog imports and price updates via verified spreadsheets.
* **Information Accessed:** Product records, variant SKUs, category trees, brand catalogs, media asset libraries.
* **Dependencies:** Clean asset storage, verified manufacturer spec sheets.
* **Related Feature IDs:** `FEAT-CAT-001`, `FEAT-BRD-001`, `FEAT-CTG-001`, `FEAT-DAT-001`.

---

### 2.4 ACT-004: Inventory / Warehouse Operator

* **Actor Type:** Human (Logistics & Physical Operations)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Physical handling of inventory goods inside enterprise warehouses. Inwards stock from distributors, executes physical cycle counts, and inspects returned items.
* **Goals:** Ensure physical inventory matches software inventory records with zero unexplained shrinkage.
* **Permitted Business Activities:**
  * Record inward stock receipts against purchase consignments.
  * Execute manual stock adjustments (spoilage, damaged packaging, cycle count reconciliation).
  * Segregate defective or damaged inventory into quarantine locations.
  * Inspect returned customer parcels and grade them as restockable or defective.
* **Information Accessed:** Warehouse stock quantities, bin locations, purchase receipts, return inspection queues.
* **Dependencies:** Physical warehouse infrastructure, barcode scanners, clear return inspection grading standards.
* **Related Feature IDs:** `FEAT-INV-001`, `FEAT-INV-002`, `FEAT-RET-001`.

---

### 2.5 ACT-005: Order Fulfillment Specialist

* **Actor Type:** Human (Fulfillment & Dispatch)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Oversees the picking, packing, label attachment, and carrier handover of confirmed customer orders.
* **Goals:** Minimize order-to-dispatch latency and eliminate wrong-item shipping errors.
* **Permitted Business Activities:**
  * Generate warehouse picking lists and customer packing slips.
  * Mark orders as "Picked" and "Packed".
  * Request shipping labels and AWB barcodes from courier integrations.
  * Hand over consignments to courier drivers and record carrier manifest handoffs.
* **Information Accessed:** Order fulfillment queue, customer shipping addresses, ordered SKUs, AWB tracking numbers.
* **Dependencies:** Real-time order status updates, courier label generation APIs.
* **Related Feature IDs:** `FEAT-ORD-001`, `FEAT-SHP-001`.

---

### 2.6 ACT-006: Customer Support Representative

* **Actor Type:** Human (Customer Care)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Resolves customer inquiries, tracks delayed shipments, assists with order modifications prior to dispatch, and facilitates returns.
* **Goals:** Deliver prompt, empathetic, and accurate customer resolution while adhering to enterprise return and refund guidelines.
* **Permitted Business Activities:**
  * Search customer accounts by phone, email, or order number.
  * View customer order histories, tracking milestones, and payment statuses.
  * Cancel eligible unfulfilled orders at customer request.
  * Initiate manual return or replacement tickets with mandatory reason codes.
  * Add internal resolution notes to customer support tickets.
* **Information Accessed:** Customer contact details, order history, tracking updates, return ticket history, support communication logs.
* **Dependencies:** Unified customer view, order state locking rules.
* **Related Feature IDs:** `FEAT-SUP-001`, `FEAT-ORD-001`, `FEAT-RET-001`.

---

### 2.7 ACT-007: Finance & Accounting Officer

* **Actor Type:** Human (Accounting)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Audits financial transactions, reconciles payment gateway deposits, monitors refund disbursements, and validates tax liability reports.
* **Goals:** Guarantee financial transaction integrity, identify gateway settlement variances, and ensure statutory tax filing readiness.
* **Permitted Business Activities:**
  * Access payment gateway settlement reports and bank reconciliation feeds.
  * Authorize high-value customer refunds exceeding standard operational thresholds.
  * Review and download monthly tax liability reports (GST breakdowns by state).
  * Issue credit notes for returned merchandise and financial adjustments.
* **Information Accessed:** Invoices, credit notes, payment records, gateway fee deductions, refund transaction logs, tax reports.
* **Dependencies:** Accurate payment gateway webhook logs, immutable invoice records.
* **Related Feature IDs:** `FEAT-FIN-001`, `FEAT-PAY-001`, `FEAT-RPT-001`.

---

### 2.8 ACT-008: Marketing & Merchandising Manager

* **Actor Type:** Human (Commercial Growth)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Plans and configures promotional campaigns, discount coupons, clearance sales, and homepage merchandising banners.
* **Goals:** Drive customer acquisition, increase Average Order Value (AOV), and liquidate slow-moving inventory.
* **Permitted Business Activities:**
  * Create discount coupons with defined validity dates, usage limits, and minimum cart amounts.
  * Configure category-wide and brand-wide percentage discounts.
  * Configure promotional banners and featured product carousels.
  * Review campaign performance reports and coupon redemption velocity.
* **Information Accessed:** Promotion configuration tables, coupon redemption logs, sales reports by promotional code.
* **Dependencies:** Robust coupon validation engine, non-stacking protection rules.
* **Related Feature IDs:** `FEAT-PRM-001`, `FEAT-RPT-001`.

---

### 2.9 ACT-009: Registered Retail Customer

* **Actor Type:** Human (End-User)
* **Status:** `CONFIRMED`
* **Business Responsibility:** Browses the store, selects consumer electronics, saves personal delivery addresses, places orders, submits digital payments, and requests customer service.
* **Goals:** Enjoy a smooth, trustworthy, transparent, and secure online shopping experience with timely delivery and hassle-free returns.
* **Permitted Business Activities:**
  * Register, authenticate, manage profile, and maintain multiple saved addresses.
  * Search, filter, and compare electronic products and view specifications.
  * Add items to cart and wishlist.
  * Execute checkout, select delivery address, and complete payment.
  * View live order milestone tracking (Placed, Dispatched, Out for Delivery).
  * Cancel unfulfilled orders or request return/replacement within the policy window.
  * Submit product reviews and ratings for verified purchases.
* **Information Accessed:** Public product catalog, personal profile, personal order history, tracking links, invoices.
* **Dependencies:** Accessible client application, responsive payment gateway, dependable courier deliveries.
* **Related Feature IDs:** `FEAT-CUST-001`, `FEAT-CART-001`, `FEAT-CHK-001`, `FEAT-ORD-001`, `FEAT-RET-001`.

---

### 2.10 ACT-010: Guest Shopper

* **Actor Type:** Human (Unregistered End-User)
* **Status:** `PROPOSED` (Subject to Open Decision [OQ-011](./08-assumptions-dependencies-open-questions.md#oq-011))
* **Business Responsibility:** Browses the store catalog and creates temporary shopping carts without upfront account registration.
* **Goals:** Rapidly evaluate products and optionally execute checkout with minimal friction.
* **Permitted Business Activities:**
  * Search catalog, view product specifications, and compare items.
  * Add items to a temporary guest cart stored via browser session/cookie.
  * Optionally convert to a registered account upon order placement.
* **Information Accessed:** Public product catalog, session cart contents.
* **Dependencies:** Session management mechanisms, guest-to-customer cart merge logic.
* **Related Feature IDs:** `FEAT-CAT-001`, `FEAT-CART-001`, `FEAT-CHK-001`.

---

### 2.11 ACT-011: External Payment Gateway (Provider System)

* **Actor Type:** External System / Financial Utility
* **Status:** `CONFIRMED`
* **Business Responsibility:** Provides PCI-DSS compliant payment collection services, card tokenization, UPI intent generation, net banking redirection, and automated webhook notifications.
* **Goals:** Provide maximum transaction authorization rates with sub-second response times and robust fraud screening.
* **Permitted Business Activities:**
  * Accept payment authorization and capture requests from the Chellaa platform.
  * Send asynchronous webhook callbacks regarding transaction outcomes (Success, Failure, Pending, Chargeback).
  * Process API-directed refund requests back to customer source accounts.
* **Information Accessed:** Encrypted transaction amount, order reference ID, masked customer identifier.
* **Dependencies:** Banking networks, card associations, UPI rails.
* **Related Feature IDs:** `FEAT-PAY-001`, `FEAT-PAY-002`, `FEAT-INT-001`.

---

### 2.12 ACT-012: External Courier Logistics Provider (Provider System)

* **Actor Type:** External System / Freight Partner
* **Status:** `CONFIRMED`
* **Business Responsibility:** Provides physical transportation of parcels from the enterprise warehouse to the customer's delivery doorstep.
* **Goals:** Safe, on-time transit, verified doorstep delivery, and automated tracking event publication.
* **Permitted Business Activities:**
  * Provide real-time PIN-code serviceability and rate quotation APIs.
  * Receive consignment booking requests and issue Air Waybill (AWB) numbers.
  * Transmit webhook tracking events (Picked Up, In-Transit, Out for Delivery, Delivered, Undelivered/RTO).
  * Collect Cash on Delivery (COD) funds where authorized and remit to enterprise bank.
* **Information Accessed:** Recipient name, masked phone number, delivery address, parcel weight and dimensions.
* **Dependencies:** Physical vehicle fleet, local delivery personnel, mobile scanner apps.
* **Related Feature IDs:** `FEAT-SHP-001`, `FEAT-INT-001`.

---

### 2.13 ACT-013: External Messaging Communications Gateway (Provider System)

* **Actor Type:** External System / Telecommunications Relay
* **Status:** `CONFIRMED`
* **Business Responsibility:** Relays transactional notifications across SMS, email (SMTP/API), and WhatsApp Business API.
* **Goals:** Ensure 99.99% delivery reliability for time-sensitive security OTPs and order status updates within 5 seconds.
* **Permitted Business Activities:**
  * Ingest outbound notification payloads containing customer recipient addresses and rendered message text.
  * Transmit delivery receipts (Delivered, Bounced, Rejected) back to the Chellaa platform.
* **Information Accessed:** Recipient phone number, email address, message body content.
* **Dependencies:** Telecom carriers, WhatsApp server infrastructure, anti-spam filters.
* **Related Feature IDs:** `FEAT-NTF-001`, `FEAT-INT-001`.

---

### 2.14 ACT-014: Observability & SRE Operations Personnel

* **Actor Type:** Human / Automated Operations Agent
* **Status:** `CONFIRMED`
* **Business Responsibility:** Monitors system health, distributed traces, background job execution, API latencies, and transaction error rates.
* **Goals:** Detect service degradation proactively, prevent cascading outages, and maintain business operational continuity.
* **Permitted Business Activities:**
  * View operational health dashboards, Prometheus metrics, and distributed OpenTelemetry traces.
  * Receive automated alerts regarding high error rates, payment failures, or delayed background jobs.
  * Initiate emergency circuit breaker trips or maintenance mode activations.
* **Information Accessed:** Anonymized system logs, metrics, trace IDs, health check endpoints.
* **Dependencies:** Centralized logging, metric collectors, tracing instrumentation.
* **Related Feature IDs:** `FEAT-OPS-001`, `FEAT-BCM-001`.

---

## 3. Actor Responsibility & Security Boundaries Matrix

| Actor ID          | Role Category     | Authentication Method                 | Principle of Least Privilege Boundary                                                                                    |
| :---------------- | :---------------- | :------------------------------------ | :----------------------------------------------------------------------------------------------------------------------- |
| **ACT-001** | Executive         | MFA + Strong Password                 | Full read access across all domains; approval-only access for high-value financial actions.                              |
| **ACT-002** | Administrator     | MFA + Strong Password                 | Full user and configuration access; zero access to customer passwords or raw payment tokens.                             |
| **ACT-003** | Catalog Staff     | Single Sign-On / Password             | Read/Write access to catalog, categories, brands, specifications; zero access to orders or financial ledgers.            |
| **ACT-004** | Warehouse Staff   | Role Password / PIN                   | Read/Write access to physical stock counts, receiving, pick-lists; zero access to customer financial data.               |
| **ACT-005** | Fulfillment Staff | Role Password / PIN                   | Read access to confirmed orders; write access to packing and dispatch status; zero access to catalog pricing.            |
| **ACT-006** | Support Staff     | MFA + Role Password                   | Read access to customer orders and tracking; restricted write access to support notes and return requests.               |
| **ACT-007** | Finance Staff     | MFA + Strong Password                 | Read/Write access to payments, invoices, refunds, tax summaries; zero access to product publishing.                      |
| **ACT-008** | Marketing Staff   | Role Password                         | Read/Write access to coupons, promotions, banners; zero access to inventory counts or system configs.                    |
| **ACT-009** | Customer          | Mobile OTP / Password                 | Read/Write access strictly bounded to their own customer profile, cart, orders, and addresses.                           |
| **ACT-010** | Guest Shopper     | Anonymous Session Token               | Read access to catalog; write access to temporary session cart; zero access to historical orders.                        |
| **ACT-011** | External System   | Mutual TLS / API Key / HMAC Signature | Restricted to payment initiation, verification, and webhook endpoints with strict replay validation.                     |
| **ACT-012** | External System   | API Key / Webhook HMAC                | Restricted to shipping consignment booking and logistics webhook endpoints.                                              |
| **ACT-013** | External System   | Secure API Gateway Relay              | Restricted to outbound notification dispatch and delivery receipt status webhooks.                                       |
| **ACT-014** | SRE / Ops         | VPN / Mutual Auth                     | Read access to monitoring metrics and telemetry data; zero access to customer personally identifiable information (PII). |
