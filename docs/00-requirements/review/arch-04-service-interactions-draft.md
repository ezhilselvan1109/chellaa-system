# ARCH-04 — Service Interaction Workflows & Dynamic Collaboration Diagrams

> [!WARNING]
> **NON-BINDING — DRAFT ONLY — NOT APPROVED FOR IMPLEMENTATION**  
> This specification is an exploratory Phase 2 architecture drafting instrument prepared under `docs/00-requirements/review/`. It does **not** constitute an approved architecture design, does not commit the enterprise to physical infrastructure or frameworks, and does not authorize software implementation.  
> Transitioning to approved architecture under `docs/01-architecture/ARCH-04-service-interactions.md` remains strictly blocked until the authorized Enterprise Business Owner signs off on [`docs/00-requirements/11-review-and-approval.md`](../11-review-and-approval.md) and ratifies the strategic decisions in [`docs/00-requirements/review/executive-decision-register.md`](./executive-decision-register.md).

---

## 1. Document Control

| Document Attribute | Specification Details |
| :--- | :--- |
| **Document ID** | `ARCH-04` |
| **Document Title** | Service Interaction Workflows, Dynamic Collaboration Diagrams, and Saga Sequences |
| **Project Name** | Chellaa E-Commerce Platform |
| **Repository & Branch** | [`ezhilselvan1109/chellaa-system`](https://github.com/ezhilselvan1109/chellaa-system) &bull; Branch: `dev` |
| **File Location** | `docs/00-requirements/review/arch-04-service-interactions-draft.md` |
| **Version** | `1.0.0-DRAFT` |
| **Lifecycle State** | **`DRAFT_COMPLETE_PENDING_REVIEW`** (Non-Binding Preparatory Draft) |
| **Target Promotion Path** | `docs/01-architecture/ARCH-04-service-interactions.md` (Deferred pending formal executive sign-off) |
| **Author Roles** | Principal Integration & API Architect, Distributed Systems Engineer & Solution Architect |
| **Target Reviewers** | Chief Technology Officer, Backend Engineering Leads, QA Automation Lead & Lead Business Analyst |
| **Upstream Baseline Documents** | [`06-business-workflows.md`](../06-business-workflows.md) (`WF-001`–`017`), [`04-functional-requirements.md`](../04-functional-requirements.md), [`05-business-rules.md`](../05-business-rules.md) |
| **Upstream Architecture Artifacts** | [`arch-01-architecture-overview-draft.md`](./arch-01-architecture-overview-draft.md), [`arch-02-domain-decomposition-draft.md`](./arch-02-domain-decomposition-draft.md), [`arch-03-service-decomposition-draft.md`](./arch-03-service-decomposition-draft.md), [`architecture-decision-analysis.md`](./architecture-decision-analysis.md) (`AD-004`) |

### 1.1 Document Lifecycle & Governance Checkpoint

The Chellaa technical architecture governance framework enforces a strict six-stage lifecycle gate. The current progress status for `ARCH-04` is recorded below:

| Lifecycle Stage | Stage Status | Evaluation Criteria & Operational Verification |
| :--- | :---: | :--- |
| **1. Draft Completion** | **`PENDING_RECONCILIATION`** | Existing sequence diagrams require ID/title reconciliation against the current 17-workflow requirements inventory; the current draft does not yet demonstrate complete coverage. |
| **2. Requirements Traceability** | **`PENDING_RECONCILIATION`** — Previous completeness claims are withdrawn until source IDs and bidirectional mappings are verified against the current baseline (17 workflows). |
| **3. Technical Architecture Review** | **`PENDING_REVIEW`** | Subject to peer review by CTO, Lead Integration Architect, and Backend Engineering Leads. |
| **4. Business Owner Policy Review** | **`PENDING_REVIEW`** | Subject to review of late payment workflows ([`ADQ-003`](./executive-decision-register.md#adq-003)) and reverse logistics policies ([`ADQ-004`](./executive-decision-register.md#adq-004)). |
| **5. Formal Baseline Approval** | **`NOT_APPROVED`** | Maintained strictly per Document 11 governance gate ([`11-review-and-approval.md`](../11-review-and-approval.md)). |
| **6. Implementation Authorization** | **`NOT_GRANTED`** | Strictly blocked pending Business Owner signature in Document 11 Section 7. |

### 1.2 Revision History

| Version | Date | Author Roles | Description of Changes & Governance Triggers |
| :---: | :---: | :--- | :--- |
| `0.1.0-PLAN` | 2026-10-18 | Principal Software Architect | Preliminary workflow interaction index in [`phase-2-architecture-planning.md`](./phase-2-architecture-planning.md). |
| `0.2.0-DEC` | 2026-10-18 | Distributed Systems Architect | Saga orchestration analysis (`AD-004`) in [`architecture-decision-analysis.md`](./architecture-decision-analysis.md). |
| `0.3.0-REG` | 2026-10-24 | Architecture Governance Lead | Detailed specification plan, sequence validation rules, and acceptance gates in [`phase-2-document-status-register.md`](./phase-2-document-status-register.md). |
| `1.0.0-DRAFT` | 2026-10-24 | Principal Integration Architect | `ARCH-04` review update: the source requirements now contain 17 workflows; the sequence diagrams and mapping table require a full identifier and coverage reconciliation. |

---

## 2. Interaction Design & Orchestration Principles

This document specifies the runtime dynamic interactions across the 5 Coarse-Grained Service Clusters defined in [`ARCH-03`](./arch-03-service-decomposition-draft.md) and external actors. All interactions adhere to three core architectural principles:

1. **Synchronous Request-Response Boundary:** Used strictly when immediate consistency or client blocking is required (e.g., authentication handshake, in-memory coupon validation, 15-minute atomic stock hold reservation). Protocols: HTTPS REST HTTP/2 JSON for external clients; internal gRPC for cluster-to-cluster calls.
2. **Asynchronous Event-Driven Decoupling:** Used for downstream lifecycle progression, notification dispatches, search indexing, and auditing. Published via Transactional Outbox CDC to Kafka / CloudEvents v1.0.
3. **Compensating Saga Orchestration ([`AD-004`](./architecture-decision-analysis.md#ad-004)):** Multi-cluster transactions (Checkout $\leftrightarrow$ Hold $\leftrightarrow$ Payment) execute under a central Saga Orchestrator inside the *Commerce Cluster*. If an downstream step fails, explicit semantic compensating actions roll back prior state (e.g., releasing holds, initiating refunds).

---

## 3. Existing End-to-End Sequence Diagrams — Coverage Reconciliation Required

---

### 3.1 WF-001: Customer Registration, Phone OTP & Session Merge

* **Scope:** Customer account registration, phone number OTP verification, and guest session cart merging upon login ([`FEAT-CUST-001`](../03-business-feature-inventory.md#feat-cust-001), [`FEAT-CART-001`](../03-business-feature-inventory.md#feat-cart-001), [`BR-CART-001`](../05-business-rules.md#br-cart-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009 / ACT-010)
  participant APIGW as API Gateway
  participant Commerce as Commerce Cluster (01. Cust / 06. Cart)
  participant Telecom as Telecom Gateway (DEP-003)
  participant EventBus as Event Bus (Kafka)
  participant Audit as Platform Cluster (20. Audit)

  Customer->>APIGW: POST /api/v1/auth/register-otp (Phone)
  APIGW->>Commerce: Generate OTP & Challenge Token
  Commerce->>Telecom: Dispatch SMS OTP (Template DLT)
  Telecom-->>Customer: Deliver 6-digit OTP SMS
  Customer->>APIGW: POST /api/v1/auth/verify-otp (OTP, GuestCartID)
  APIGW->>Commerce: Verify OTP & Activate Profile
  Commerce->>Commerce: Merge Guest Cart Items to Customer Cart
  Commerce->>EventBus: Publish customer.registered & cart.merged
  EventBus->>Audit: Ingest WORM Audit Event Block
  Commerce-->>Customer: Return Auth Token (JWT) & Merged Cart DTO
```

---

### 3.2 WF-004: Product Discovery, Search & Filtering

* **Scope:** Full-text typo-tolerant search, faceted filtering, and stockout demotion ([`FEAT-SRCH-001`](../03-business-feature-inventory.md#feat-srch-001), [`BR-SRCH-001`](../05-business-rules.md#br-srch-001), [`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Shopper (ACT-009)
  participant APIGW as API Gateway
  participant Search as Elasticsearch Read Engine (05. Search)
  participant EventBus as Event Bus (Kafka)
  participant Merchandising as Merchandising Cluster (03. Catalog)

  Customer->>APIGW: GET /api/v1/search?q=smartphone&ram=8GB
  APIGW->>Search: Execute Typo-Tolerant Faceted Query
  Search-->>APIGW: Return Paginated Results (In-Stock First, Out-of-Stock Demoted)
  APIGW-->>Customer: Display Catalog Page (p95 <= 200ms)

  Note over Merchandising,Search: Asynchronous Index Sync (<= 5s SLA)
  Merchandising->>EventBus: Publish product.published
  EventBus->>Search: Upsert Elasticsearch Document
```

---

### 3.3 WF-005: Shopping Cart Management & Price/Stock Revalidation

* **Scope:** Adding SKUs to shopping cart and enforcing maximum purchase limits ([`FEAT-CART-001`](../03-business-feature-inventory.md#feat-cart-001), [`BR-CART-001`](../05-business-rules.md#br-cart-001), [`BR-CART-002`](../05-business-rules.md#br-cart-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Shopper (ACT-009)
  participant APIGW as API Gateway
  participant Commerce as Commerce Cluster (06. Cart Domain)

  Customer->>APIGW: POST /api/v1/cart/items (SKU, Qty=3)
  APIGW->>Commerce: Validate Item Quantity against SKU Max Cap
  alt Requested Quantity > Max SKU Cap (e.g. Max 2 Units)
    Commerce-->>Customer: 400 Bad Request ("Exceeds maximum purchase limit")
  else Quantity Valid
    Commerce->>Commerce: Append Item to ShoppingCart Aggregate
    Commerce-->>Customer: 200 OK (Updated Cart Summary DTO)
    Note over Commerce: Invariant: Cart addition does NOT reserve inventory hold
  end
```

---

### Supporting Flow: Wishlist Management & Price Watch (part of cart capability; no separate WF ID)

* **Scope:** Adding/removing items to authenticated customer wishlist ([`FEAT-WSH-001`](../03-business-feature-inventory.md#feat-wsh-001), [`FR-WSH-001`](../04-functional-requirements.md#fr-wsh-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  participant APIGW as API Gateway
  participant Commerce as Commerce Cluster (06. Wishlist Domain)

  Customer->>APIGW: POST /api/v1/wishlist/items (SKU)
  APIGW->>Commerce: Authenticate JWT & Append SKU to Wishlist
  Commerce-->>Customer: 201 Created (Saved Items List)
```

---

### 3.5 WF-011: Promotions & Coupon Redemption

* **Scope:** Applying discount coupon codes, verifying non-stacking rules, and pro-rata line-item allocation ([`FEAT-PRM-001`](../03-business-feature-inventory.md#feat-prm-001), [`BR-PRM-001`](../05-business-rules.md#br-prm-001), [`BR-PRM-002`](../05-business-rules.md#br-prm-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  participant APIGW as API Gateway
  participant Commerce as Commerce Cluster (04. Pricing & Promotions)

  Customer->>APIGW: POST /api/v1/cart/apply-coupon (Code="SAVE10")
  APIGW->>Commerce: Evaluate Coupon Invariants
  alt Existing Coupon Already Applied
    Commerce-->>Customer: 400 Bad Request ("Multiple coupons cannot be stacked")
  else Order Below MOV or Expired
    Commerce-->>Customer: 400 Bad Request ("Minimum order value not met")
  else Valid Coupon
    Commerce->>Commerce: Calculate Basket Discount
    Commerce->>Commerce: Distribute Discount Pro-Rata Across Eligible Line Items
    Commerce-->>Customer: 200 OK (Discount Breakdown DTO)
  end
```

---

### 3.6 WF-006: Multi-Step Checkout & 15-Minute Atomic Hold Orchestration

* **Scope:** Initiating checkout, acquiring 15-minute atomic stock holds, and server-side total recalculation ([`FEAT-CHK-001`](../03-business-feature-inventory.md#feat-chk-001), [`BR-INV-001`](../05-business-rules.md#br-inv-001), [`BR-CHK-001`](../05-business-rules.md#br-chk-001), [`BR-CHK-002`](../05-business-rules.md#br-chk-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  participant APIGW as API Gateway
  participant Commerce as Commerce Cluster (08. Checkout Saga)
  participant Fulfillment as Fulfillment Cluster (07. Inventory ATS)
  participant Finance as Finance Cluster (10. Payments)

  Customer->>APIGW: POST /api/v1/checkout/initiate (AddressID, Items)
  APIGW->>Commerce: Start Checkout Saga Session
  Commerce->>Commerce: Recalculate Totals Server-Side (Ignore Client Prices)
  Commerce->>Fulfillment: gRPC: AcquireStockHold(SessionID, SKU, Qty)
  alt Available-to-Sell (ATS) Stock Insufficient
    Fulfillment-->>Commerce: Error: InsufficientStock
    Commerce-->>Customer: 409 Conflict ("SKU out of stock, hold failed")
  else ATS Stock Available
    Fulfillment->>Fulfillment: Atomically Lock StockHold (15-min TTL)
    Fulfillment-->>Commerce: Success: HoldAcquired(ExpiresIn=900s)
    Commerce->>Finance: gRPC: CreatePaymentIntent(SessionID, TotalAmount)
    Finance-->>Commerce: Return PaymentIntent(GatewaySessionID)
    Commerce-->>Customer: 200 OK (Payment Intent & 15-min Countdown)
  end
```

---

### 3.7 WF-007: Payment Processing, Late Webhook Recovery & Tax Invoicing

* **Scope:** Payment capture webhook, late webhook recovery, sequential tax invoice generation ([`FEAT-PAY-001`](../03-business-feature-inventory.md#feat-pay-001), [`BR-PAY-001`](../05-business-rules.md#br-pay-001), [`BR-PAY-002`](../05-business-rules.md#br-pay-002), [`BR-FIN-001`](../05-business-rules.md#br-fin-001), [`BR-FIN-003`](../05-business-rules.md#br-fin-003)).

```mermaid
sequenceDiagram
  autonumber
  actor Gateway as Razorpay / Cashfree (ACT-011)
  participant Finance as Finance Cluster (10. Pay / 11. Tax)
  participant Commerce as Commerce Cluster (08. Checkout / 09. Orders)
  participant Fulfillment as Fulfillment Cluster (07. Inventory)
  participant EventBus as Event Bus (Kafka)
  participant Platform as Platform Cluster (14. Ntf / 20. Audit)

  Gateway->>Finance: HTTPS Webhook: payment.captured (HMAC-SHA256)
  Finance->>Finance: Verify HMAC-SHA256 Signature
  Finance->>Commerce: Query Hold Status (CheckoutSessionID)
  alt Stock Hold Expired (> 15 minutes elapsed)
    Note over Finance: Dual-Path Late Webhook Recovery (BR-PAY-002)
    Finance->>Gateway: API: Dispatch Full Refund to Source Instrument
    Finance->>EventBus: Publish refund.initiated (LateWebhookExpiry)
    Finance-->>Gateway: 200 OK (Refund Dispatched)
  else Stock Hold Active
    Finance->>Commerce: Complete Checkout -> Create CustomerOrder
    Commerce->>Fulfillment: Commit StockHold to Permanent Stock Deduction
    Finance->>Finance: Allocate Monotonic Gapless Tax Invoice (INV/YYYY-YY/XXXXX)
    Finance->>EventBus: Publish payment.captured & invoice.generated
    EventBus->>Platform: Dispatch Order Confirmation SMS / Email
    Finance-->>Gateway: 200 OK (Processed)
  end
```

---

### 3.8 WF-008: Warehouse Pick, Serial Scanning & Consignment Booking

* **Scope:** Pick-and-pack fulfillment, mandatory serial/IMEI scanning, carrier consignment booking ([`FEAT-INV-003`](../03-business-feature-inventory.md#feat-inv-003), [`FEAT-SHP-001`](../03-business-feature-inventory.md#feat-shp-001), [`BR-INV-002`](../05-business-rules.md#br-inv-002), [`BR-SHP-001`](../05-business-rules.md#br-shp-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Operator as Warehouse Operator (ACT-004)
  participant Fulfillment as Fulfillment Cluster (07. Inv / 12. Ship)
  participant Shipping as Shipping Management System / approved provider adapter
  participant Commerce as Commerce Cluster (09. Orders)
  participant EventBus as Event Bus (Kafka)

  Operator->>Fulfillment: Retrieve Picking List for Order
  Operator->>Fulfillment: Scan Barcode / IMEI / Serial Number
  Fulfillment->>Fulfillment: Validate Serial against SKU Catalog Spec
  Fulfillment->>Courier: REST: BookConsignment(Weight, Dims, Address)
  Courier-->>Fulfillment: Return AWB Number & Thermal Shipping Label URL
  Fulfillment->>Commerce: Advance Order Status -> PACKED
  Fulfillment->>EventBus: Publish shipment.booked
```

---

### Supporting Flow: Shipment Tracking, Delivery Updates & Return-to-Origin (supports WF-008; no separate WF ID)

* **Scope:** Carrier tracking status webhook ingestion, 3-attempt delivery tracking, and automatic RTO execution ([`FEAT-SHP-001`](../03-business-feature-inventory.md#feat-shp-001), [`BR-SHP-002`](../05-business-rules.md#br-shp-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Courier as 3PL Courier Partner (ACT-012)
  participant Fulfillment as Fulfillment Cluster (12. Shipping)
  participant Commerce as Commerce Cluster (09. Orders)
  participant EventBus as Event Bus (Kafka)
  participant Platform as Platform Cluster (14. Notifications)

  Courier->>Fulfillment: HTTPS Webhook: tracking.update (AWB, Status)
  alt Status == DELIVERED
    Fulfillment->>Commerce: Advance Order Status -> DELIVERED
    Fulfillment->>EventBus: Publish order.delivered (Starts 7-day return window)
    EventBus->>Platform: Dispatch Delivery Confirmation SMS
  else Delivery Attempt Failed (Attempt < 3)
    Fulfillment->>Fulfillment: Increment Attempt Counter
    Fulfillment->>EventBus: Publish shipment.delivery_delayed
  else Delivery Attempt Failed (Attempt == 3)
    Note over Fulfillment: Max 3 Attempts Exceeded (BR-SHP-002)
    Fulfillment->>Fulfillment: Transition Status -> RTO_INITIATED
    Fulfillment->>Commerce: Advance Order Status -> RTO_IN_TRANSIT
    Fulfillment->>EventBus: Publish shipment.rto_initiated
    EventBus->>Platform: Alert Customer of Return-to-Origin
  end
```

---

### 3.10 WF-010: Returns Request, Reverse Logistics, Diagnostic Gate & Refund

* **Scope:** 7-day return window validation, reverse pickup, mandatory technician diagnostic condition grading gate, source-instrument refund, and credit note generation ([`FEAT-RET-001`](../03-business-feature-inventory.md#feat-ret-001), [`BR-RET-001`](../05-business-rules.md#br-ret-001)–[`003`](../05-business-rules.md#br-ret-003), [`BR-FIN-002`](../05-business-rules.md#br-fin-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  actor Tech as Certified Technician (ACT-004)
  participant Fulfillment as Fulfillment Cluster (13. Returns)
  participant Finance as Finance Cluster (10. Pay / 11. Tax)
  participant Gateway as Payment Gateway (Razorpay)
  participant EventBus as Event Bus (Kafka)

  Customer->>Fulfillment: POST /api/v1/returns (OrderID, SKU, Reason)
  Fulfillment->>Fulfillment: Check Delivery Date (Window <= 7 Days)
  Fulfillment-->>Customer: Return Request Accepted -> Reverse AWB Booked
  Note over Fulfillment: Reverse Consignment Inwarded at Warehouse
  Tech->>Fulfillment: POST /api/v1/returns/diagnostic (ConditionGrade, Passed)
  alt Diagnostic Check Failed (Physical Damage / Counterfeit)
    Fulfillment->>Fulfillment: Reject Return Request
    Fulfillment->>EventBus: Publish return.rejected
  else Diagnostic Check Passed (Functional / Unopened)
    Fulfillment->>Fulfillment: Approve Return Request
    Fulfillment->>EventBus: Publish return.graded (Passed=true)
    EventBus->>Finance: Consume return.graded
    Finance->>Finance: Generate Sequential Credit Note (CN/YYYY-YY/XXXXX)
    Finance->>Gateway: API: Dispatch Full Refund to Original Source Instrument
    Finance->>EventBus: Publish refund.completed
  end
```

---

### 3.11 WF-002: Product Catalog Management & Publishing

* **Scope:** Create and validate product records, category/brand associations, variant/SKU attributes, media references, and publish/unpublish state; the public search flow is mapped separately to WF-004 ([`FEAT-CAT-001`](../03-business-feature-inventory.md#feat-cat-001), [`FEAT-CAT-002`](../03-business-feature-inventory.md#feat-cat-002), [`FR-CAT-001`](../04-functional-requirements.md#fr-cat-001), [`FR-CAT-003`](../04-functional-requirements.md#fr-cat-003), [`BR-CAT-001`](../05-business-rules.md#br-cat-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Merch as Merchandising / Catalog Staff
  participant Admin as Admin API
  participant Catalog as Catalog Domain
  participant Media as Media/Object Storage
  participant Events as Event Bus
  participant Search as Search Index

  Merch->>Admin: Create or update product / variant
  Admin->>Catalog: Validate required fields, brand, category, SKU and variant attributes
  Catalog->>Media: Verify approved media references
  Media-->>Catalog: Media reference validation
  alt Validation fails
    Catalog-->>Merch: Field-level validation errors
  else Validation succeeds
    Catalog->>Catalog: Persist draft and audit change
    Merch->>Catalog: Request publish
    Catalog->>Catalog: Check publish prerequisites and status
    Catalog->>Events: Publish catalog.product-published
    Events-->>Search: Update searchable product document
    Catalog-->>Merch: Publish result and version
  end
```

---

### 3.12 WF-003: Inventory Receiving & Stock Adjustment

* **Scope:** Receive purchase/stock-in records, validate SKU and quantity, record warehouse custody and stock adjustments with audit reasons, and update inventory availability ([`FEAT-INV-001`](../03-business-feature-inventory.md#feat-inv-001), [`FR-INV-001`](../04-functional-requirements.md#fr-inv-001), [`FR-INV-003`](../04-functional-requirements.md#fr-inv-003), [`BR-INV-001`](../05-business-rules.md#br-inv-001), [`BR-INV-003`](../05-business-rules.md#br-inv-003)).

```mermaid
sequenceDiagram
  autonumber
  actor Operator as Warehouse Operator
  participant WMS as Chellaa Inventory / Warehouse Domain
  participant Catalog as Catalog Domain
  participant Ledger as Stock Ledger
  participant Audit as Audit Service
  participant Events as Event Bus

  Operator->>WMS: Submit receipt or stock adjustment
  WMS->>Catalog: Validate active SKU / variant
  Catalog-->>WMS: SKU validation result
  WMS->>WMS: Validate quantity, warehouse, reason and serial/IMEI where applicable
  alt Invalid SKU or adjustment data
    WMS-->>Operator: Reject with validation reason
  else Valid stock movement
    WMS->>Ledger: Append idempotent stock movement
    WMS->>Audit: Record actor, reason, source and before/after values
    WMS->>Events: Publish inventory.received / inventory.adjusted
    WMS-->>Operator: Receipt/adjustment reference
  end
```

---

### 3.13 WF-009: Order Cancellation & Inventory Release

* **Scope:** Validate cancellation eligibility against order state, idempotently cancel eligible orders, release reserved inventory, and initiate the applicable payment/refund follow-up without treating shipment tracking as the cancellation workflow ([`FEAT-ORD-001`](../03-business-feature-inventory.md#feat-ord-001), [`FEAT-INV-002`](../03-business-feature-inventory.md#feat-inv-002), [`FR-ORD-002`](../04-functional-requirements.md#fr-ord-002), [`BR-ORD-002`](../05-business-rules.md#br-ord-002), [`BR-INV-002`](../05-business-rules.md#br-inv-002)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer / Support Agent
  participant Orders as Order Domain
  participant Inventory as Inventory Domain
  participant Payments as Payment Domain
  participant Events as Event Bus

  Customer->>Orders: Request cancellation (order ID, idempotency key)
  Orders->>Orders: Validate order state and cancellation policy
  alt Cancellation not permitted
    Orders-->>Customer: Reject with current state and reason
  else Cancellation permitted
    Orders->>Orders: Record cancellation transition idempotently
    Orders->>Inventory: Release unconsumed stock reservation
    Inventory-->>Orders: Release result
    opt Payment captured or payment outcome requires reconciliation
      Orders->>Payments: Initiate policy-governed refund/reconciliation
      Payments-->>Orders: Refund/reconciliation reference
    end
    Orders->>Events: Publish order.cancelled
    Orders-->>Customer: Cancellation result and next-step status
  end
```

---

### 3.14 WF-014: Reporting and Daily Financial Reconciliation

* **Scope:** Reconciling captured payment transactions, invoiced ledgers, and bank settlement batches ([`FEAT-PAY-002`](../03-business-feature-inventory.md#feat-pay-002), [`FEAT-RPT-001`](../03-business-feature-inventory.md#feat-rpt-001), [`BR-RPT-001`](../05-business-rules.md#br-rpt-001)).

```mermaid
sequenceDiagram
  autonumber
  participant Gateway as Payment Gateway (Razorpay)
  participant Finance as Finance Cluster (10. Payments)
  participant Platform as Platform Cluster (17. Reporting)

  Gateway->>Finance: Upload Daily Settlement Batch File (CSV)
  Finance->>Finance: Match Gateway Settlement IDs against PaymentTransactions
  Finance->>Finance: Calculate Gross Sales, MDR Fees, GST Withheld, Net Deposit
  Finance->>Platform: Export DailyReconciliationSummary to Read Replica Datamart
  Note over Platform: Zero read contention on online transactional write database
```

---

### 3.15 WF-017: Verified-Buyer Review Submission & Moderation

* **Scope:** Enforcing verified-purchaser review gating, abuse filtering, and star rating aggregation ([`FEAT-REV-001`](../03-business-feature-inventory.md#feat-rev-001), [`BR-REV-001`](../05-business-rules.md#br-rev-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  participant APIGW as API Gateway
  participant Platform as Platform Cluster (16. Reviews Domain)
  participant Commerce as Commerce Cluster (09. Orders Domain)
  participant EventBus as Event Bus (Kafka)
  participant Search as Elasticsearch Read Engine

  Customer->>APIGW: POST /api/v1/reviews (SKU, Rating, Comment)
  APIGW->>Platform: Authenticate Customer JWT
  Platform->>Commerce: gRPC: CheckVerifiedPurchase(CustomerID, SKU)
  alt No Verified Delivered Order Found
    Commerce-->>Platform: Verified=false
    Platform-->>Customer: 403 Forbidden ("Only verified buyers may review items")
  else Verified Delivered Order Exists
    Commerce-->>Platform: Verified=true
    Platform->>Platform: Run Profanity Regex Filter & Stage Review
    Platform->>EventBus: Publish review.submitted
    Note over Platform: Staff approves review in moderation queue
    Platform->>EventBus: Publish rating.aggregated (New Average Score)
    EventBus->>Search: Update SKU Average Rating Badge
    Platform-->>Customer: 201 Created ("Review submitted for moderation")
  end
```

---

### 3.16 WF-013: Customer Support Ticket Intake & Transit Damage Dispute

* **Scope:** Customer support ticketing, transit dispute intake, and 2-hour critical SLA escalation ([`FEAT-SUP-001`](../03-business-feature-inventory.md#feat-sup-001), [`BR-SUP-001`](../05-business-rules.md#br-sup-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Customer as Customer (ACT-009)
  actor Agent as Support Agent (ACT-006)
  participant Platform as Platform Cluster (15. Customer Support)
  participant Fulfillment as Fulfillment Cluster (12. Shipping)

  Customer->>Platform: POST /api/v1/support/tickets (OrderID, Issue="DAMAGED_BOX")
  Platform->>Platform: Categorize Ticket & Start 2-Hour SLA Timer
  Platform->>Fulfillment: Query AWB Tracking Checkpoints & Courier Log
  Platform->>Agent: Route Ticket to Agent Queue (Customer Phone Masked)
  Agent->>Platform: Post Resolution (Initiate Return Inspection Request)
```

---

### 3.17 WF-012: Transactional Notification Dispatch

* **Scope:** Multi-channel message queuing, TRAI DLT template validation, and dispatch bypass of promotional opt-outs ([`FEAT-NTF-001`](../03-business-feature-inventory.md#feat-ntf-001), [`BR-NTF-001`](../05-business-rules.md#br-ntf-001)).

```mermaid
sequenceDiagram
  autonumber
  participant EventBus as Event Bus (Kafka)
  participant Platform as Platform Cluster (14. Notifications)
  participant Telecom as Telecom Gateway (SMS/WhatsApp)

  EventBus->>Platform: Consume order.confirmed / shipment.dispatched
  Platform->>Platform: Load Registered TRAI DLT Template ID
  Platform->>Platform: Inject Dynamic Variables (Order ID, Amount, AWB)
  Platform->>Telecom: Outbound HTTP: Dispatch Transactional SMS
  Telecom-->>Platform: Return Delivery Receipt (MessageID, Status)
```

---

### 3.18 WF-015: External Provider Failure and Recovery

* **Scope:** Detecting and containing external payment, shipping, and messaging provider failures without inventing automatic provider failover or unapproved retry/fallback policy ([`FEAT-INT-001`](../03-business-feature-inventory.md#feat-int-001), [`FR-INT-001`](../04-functional-requirements.md#fr-int-001), [`BR-INT-001`](../05-business-rules.md#br-int-001)).

```mermaid
sequenceDiagram
  autonumber
  participant Caller as Calling Domain Service
  participant Adapter as Provider Adapter
  participant Guard as Circuit Breaker / Health Monitor
  participant Queue as Durable Work Queue
  participant Ops as Operations / Support

  Caller->>Adapter: Request provider operation
  Adapter->>Guard: Record outcome and evaluate health
  alt Provider operation succeeds
    Adapter-->>Caller: Provider result
  else Timeout or provider error
    Adapter->>Guard: Record failure
    Guard-->>Adapter: Current circuit state / policy
    Adapter->>Queue: Persist retryable work or reconciliation item
    Adapter->>Ops: Emit incident / degraded-provider signal
    Note over Queue,Ops: Retry timing, exhaustion, manual intervention, and any alternate-lane use require approved policy; no automatic payment-gateway failover is assumed
  end
  Ops->>Guard: Restore / validate provider health
  Guard-->>Ops: Health evidence for controlled recovery
```

---
### 3.19 WF-016: Staff RBAC Authentication, MFA & Cryptographic Audit

* **Scope:** Administrative login, mandatory TOTP MFA, 15-minute inactivity expiry, and WORM SHA-256 hash chaining ([`FEAT-ADM-001`](../03-business-feature-inventory.md#feat-adm-001), [`FEAT-AUD-001`](../03-business-feature-inventory.md#feat-aud-001), [`BR-ADM-001`](../05-business-rules.md#br-adm-001), [`BR-ADM-002`](../05-business-rules.md#br-adm-002), [`BR-AUD-001`](../05-business-rules.md#br-aud-001)).

```mermaid
sequenceDiagram
  autonumber
  actor Staff as System Admin (ACT-002)
  participant APIGW as API Gateway
  participant Platform as Platform Cluster (02. IAM / 20. Audit)

  Staff->>APIGW: POST /api/v1/admin/auth/login (Email, Password)
  APIGW->>Platform: Verify Argon2id Password Hash
  Platform-->>Staff: 200 OK (Challenge Token: MFA Required)
  Staff->>APIGW: POST /api/v1/admin/auth/mfa (ChallengeToken, TOTPCode)
  APIGW->>Platform: Validate TOTP Code
  Platform->>Platform: Issue Scoped Staff JWT (15-min Session Expiry)
  Platform->>Platform: Append AuditEventBlock (SHA-256 Chained Hash)
  Platform-->>Staff: 200 OK (Staff JWT with Claims)
```

---

## 4. Requirements Traceability Matrix for Workflows

| Business Workflow | Mapped Business Features (`FEAT`) | Mapped Functional Requirements (`FR`) | Mapped Business Rules (`BR`) | Primary Clusters Involved |
| :--- | :--- | :--- | :--- | :--- |
| **`WF-001`** (Customer Registration and Verification) | [`FEAT-CUST-001`](../03-business-feature-inventory.md#feat-cust-001), [`FEAT-CART-001`](../03-business-feature-inventory.md#feat-cart-001) | [`FR-CUST-001`](../04-functional-requirements.md#fr-cust-001), [`FR-CART-001`](../04-functional-requirements.md#fr-cart-001) | [`BR-CUST-001`](../05-business-rules.md#br-cust-001), [`BR-CART-001`](../05-business-rules.md#br-cart-001) | Cluster 2 (Commerce), Cluster 5 (Platform) |
| **`WF-002`** (Product Catalog Management & Publishing) | [`FEAT-CAT-001`](../03-business-feature-inventory.md#feat-cat-001), [`FEAT-CAT-002`](../03-business-feature-inventory.md#feat-cat-002) | [`FR-CAT-001`](../04-functional-requirements.md#fr-cat-001), [`FR-CAT-003`](../04-functional-requirements.md#fr-cat-003) | [`BR-CAT-001`](../05-business-rules.md#br-cat-001) | Cluster 1 (Merchandising), Catalog / Media, Search Index consumers |
| **`WF-003`** (Inventory Receiving & Stock Adjustment) | [`FEAT-INV-001`](../03-business-feature-inventory.md#feat-inv-001) | [`FR-INV-001`](../04-functional-requirements.md#fr-inv-001), [`FR-INV-003`](../04-functional-requirements.md#fr-inv-003) | [`BR-INV-001`](../05-business-rules.md#br-inv-001), [`BR-INV-003`](../05-business-rules.md#br-inv-003) | Cluster 3 (Fulfillment / Inventory), Audit |
| **`WF-004`** (Product Discovery, Search & Filtering) | [`FEAT-SRCH-001`](../03-business-feature-inventory.md#feat-srch-001) | [`FR-SRCH-001`](../04-functional-requirements.md#fr-srch-001), [`FR-SRCH-002`](../04-functional-requirements.md#fr-srch-002) | [`BR-SRCH-001`](../05-business-rules.md#br-srch-001), [`BR-SRCH-002`](../05-business-rules.md#br-srch-002) | Search Read Engine, Catalog read model |
| **`WF-005`** (Shopping Cart & Price/Stock Revalidation) | [`FEAT-CART-001`](../03-business-feature-inventory.md#feat-cart-001) | [`FR-CART-001`](../04-functional-requirements.md#fr-cart-001) | [`BR-CART-001`](../05-business-rules.md#br-cart-001), [`BR-CART-002`](../05-business-rules.md#br-cart-002) | Cluster 2 (Commerce), Inventory availability read |
| **`WF-006`** (Checkout and Order Placement) | [`FEAT-CHK-001`](../03-business-feature-inventory.md#feat-chk-001), [`FEAT-INV-002`](../03-business-feature-inventory.md#feat-inv-002) | [`FR-CHK-001`](../04-functional-requirements.md#fr-chk-001), [`002`](../04-functional-requirements.md#fr-chk-002), [`FR-INV-002`](../04-functional-requirements.md#fr-inv-002) | [`BR-CHK-001`](../05-business-rules.md#br-chk-001), [`002`](../05-business-rules.md#br-chk-002), [`BR-INV-001`](../05-business-rules.md#br-inv-001) | Cluster 2 (Commerce), Cluster 3 (Fulfillment) |
| **`WF-007`** (Payment Processing and Gateway Reconciliation) | [`FEAT-PAY-001`](../03-business-feature-inventory.md#feat-pay-001), [`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001) | [`FR-PAY-001`](../04-functional-requirements.md#fr-pay-001), [`FR-FIN-001`](../04-functional-requirements.md#fr-fin-001) | [`BR-PAY-001`](../05-business-rules.md#br-pay-001)–[`003`](../05-business-rules.md#br-pay-003), [`BR-FIN-001`](../05-business-rules.md#br-fin-001) | Cluster 4 (Finance), Cluster 2 (Commerce) |
| **`WF-008`** (Order Fulfillment, Packing and Dispatch) | [`FEAT-INV-003`](../03-business-feature-inventory.md#feat-inv-003), [`FEAT-SHP-001`](../03-business-feature-inventory.md#feat-shp-001) | [`FR-INV-003`](../04-functional-requirements.md#fr-inv-003), [`FR-SHP-001`](../04-functional-requirements.md#fr-shp-001) | [`BR-INV-002`](../05-business-rules.md#br-inv-002), [`BR-SHP-001`](../05-business-rules.md#br-shp-001) | Cluster 3 (Fulfillment), Cluster 2 (Commerce) |
| **`WF-009`** (Order Cancellation & Inventory Release) | [`FEAT-ORD-001`](../03-business-feature-inventory.md#feat-ord-001), [`FEAT-INV-002`](../03-business-feature-inventory.md#feat-inv-002) | [`FR-ORD-002`](../04-functional-requirements.md#fr-ord-002), [`FR-INV-002`](../04-functional-requirements.md#fr-inv-002) | [`BR-ORD-002`](../05-business-rules.md#br-ord-002), [`BR-INV-002`](../05-business-rules.md#br-inv-002) | Cluster 2 (Commerce), Cluster 3 (Inventory) |
| **`WF-010`** (Return, Replacement and Refund Processing) | [`FEAT-RET-001`](../03-business-feature-inventory.md#feat-ret-001), [`FEAT-FIN-002`](../03-business-feature-inventory.md#feat-fin-002) | [`FR-RET-001`](../04-functional-requirements.md#fr-ret-001)–[`003`](../04-functional-requirements.md#fr-ret-003) | [`BR-RET-001`](../05-business-rules.md#br-ret-001)–[`003`](../05-business-rules.md#br-ret-003), [`BR-FIN-002`](../05-business-rules.md#br-fin-002) | Cluster 3 (Fulfillment), Cluster 4 (Finance) |
| **`WF-011`** (Promotions & Coupon Redemption) | [`FEAT-PRM-001`](../03-business-feature-inventory.md#feat-prm-001) | [`FR-PRM-001`](../04-functional-requirements.md#fr-prm-001), [`FR-PRM-002`](../04-functional-requirements.md#fr-prm-002) | [`BR-PRM-001`](../05-business-rules.md#br-prm-001), [`BR-PRM-002`](../05-business-rules.md#br-prm-002) | Cluster 2 (Commerce); price maintenance remains a supporting operation |
| **`WF-012`** (Multi-Channel Notification Delivery) | [`FEAT-NTF-001`](../03-business-feature-inventory.md#feat-ntf-001) | [`FR-NTF-001`](../04-functional-requirements.md#fr-ntf-001) | [`BR-NTF-001`](../05-business-rules.md#br-ntf-001) | Cluster 5 (Platform); SMS, Email and WhatsApp Business API; fallback policy open |
| **`WF-013`** (Customer Support Ticket Resolution) | [`FEAT-SUP-001`](../03-business-feature-inventory.md#feat-sup-001) | [`FR-SUP-001`](../04-functional-requirements.md#fr-sup-001) | [`BR-SUP-001`](../05-business-rules.md#br-sup-001) | Cluster 5 (Platform), Cluster 3 (Fulfillment) |
| **`WF-014`** (Reporting and Daily Financial Reconciliation) | [`FEAT-RPT-001`](../03-business-feature-inventory.md#feat-rpt-001), [`FEAT-PAY-002`](../03-business-feature-inventory.md#feat-pay-002) | [`FR-RPT-001`](../04-functional-requirements.md#fr-rpt-001), [`FR-PAY-003`](../04-functional-requirements.md#fr-pay-003) | [`BR-RPT-001`](../05-business-rules.md#br-rpt-001), [`BR-PAY-003`](../05-business-rules.md#br-pay-003) | Cluster 4 (Finance), Cluster 5 (Platform) |
| **`WF-015`** (External Provider Failure and Recovery) | [`FEAT-INT-001`](../03-business-feature-inventory.md#feat-int-001), [`FEAT-OPS-001`](../03-business-feature-inventory.md#feat-ops-001) | [`FR-INT-001`](../04-functional-requirements.md#fr-int-001) | [`BR-INT-001`](../05-business-rules.md#br-int-001) | Provider adapters, durable work queue, operations; recovery/fallback policy requires approval |
| **`WF-016`** (Staff IAM & WORM Audit) | [`FEAT-ADM-001`](../03-business-feature-inventory.md#feat-adm-001), [`FEAT-AUD-001`](../03-business-feature-inventory.md#feat-aud-001) | [`FR-SEC-001`](../04-functional-requirements.md#fr-sec-001), [`FR-AUD-001`](../04-functional-requirements.md#fr-aud-001) | [`BR-ADM-001`](../05-business-rules.md#br-adm-001), [`BR-AUD-001`](../05-business-rules.md#br-aud-001) | Cluster 5 (Platform) |
| **`WF-017`** (Verified-Buyer Review Submission & Moderation) | [`FEAT-REV-001`](../03-business-feature-inventory.md#feat-rev-001) | [`FR-REV-001`](../04-functional-requirements.md#fr-rev-001) | [`BR-REV-001`](../05-business-rules.md#br-rev-001) | Cluster 5 (Platform), order-verification call to Cluster 2 (Commerce); moderation policy details open |

---

## 5. Review and Acceptance Checklist

The checklist below governs the quality gate audit for `ARCH-04`:

- [x] 1. **Workflow ID Coverage:** The workflow matrix and sequence diagrams now identify WF-001 through WF-017; WF-015 outage recovery and WF-011 promotion redemption were added/corrected. Semantic source-to-feature/FR/BR completeness remains a separate review gate.
- [x] 2. **Explicit Synchronous vs Asynchronous Boundary:** Sequence diagrams distinguish synchronous client/RPC blocking calls from asynchronous Kafka CloudEvents.
- [x] 3. **Semantic Compensation Paths:** Explicit rollback and compensation branches documented for checkout hold expiration, payment failure, and reverse diagnostic rejection.
- [x] 4. **Single-Retailer Anti-Scope Preserved:** Zero third-party marketplace, vendor commission, or split payout interactions present.
- [ ] 5. **Mermaid Syntax & Delimiter Balance:** Syntax validation still required in repository CI or a Mermaid renderer.
- [ ] 6. **Requirements Traceability:** Verify every sequence flow against the current BRD feature, functional requirement, business rule and workflow identifiers before claiming complete coverage.
- [x] 7. **Non-Binding Labeling:** Prominently marked as a non-binding draft pending formal Phase 1 sign-off.


---

## Cross-document reconciliation note (2026-10-24)

- Canonical workflow identifiers and titles are defined by `docs/00-requirements/06-business-workflows.md` (WF-001–WF-017). Do not infer workflow meaning from an old identifier or copy stale mapping labels.
- Recorded business decisions are inputs, not implementation approval. Online payment gateway: Razorpay only. Notification channels: SMS, Email and WhatsApp Business API; channel/fallback policy remains open. Reviews: verified buyers with admin moderation; detailed policy remains open. Advanced reporting is selected while specifications remain open. Legacy customer/order history migration is out of scope; catalog and opening-stock import remain in scope. Statutory financial records are retained for eight financial years, subject to Legal/Security review of anonymization details.
- **Shipping decision conflict:** The current OQ-005 / ADQ-014 decision register describes a hybrid provider model, while a separate owner instruction recorded in project context selects Chellaa's own Shipping Management System and excludes aggregators. This conflict is unresolved; no named courier/aggregator integration or provider-routing policy is approved by this note. Resolve the source decision before architecture sign-off.
- This draft remains non-binding. Phase 1 review is `PENDING_APPROVAL`, the requirements baseline is `NOT_APPROVED`, and architecture implementation authorization is `NOT_GRANTED`. This note does not authorize promotion to `docs/01-architecture/`.
