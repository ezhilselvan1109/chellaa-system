# Business Requirements Document (BRD)
## Document 08: Assumptions, Dependencies, and Open Questions

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | BRD-DOC-08 |
| **Document Title** | Enterprise Assumptions, Dependencies, and Open Questions Registers |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Architecture** | Backend-First Microservices Ecosystem (Phase 1: Requirements Baseline) |
| **Business Model** | Single-Retailer Direct-to-Consumer (D2C) & Business-to-Consumer (B2C) |
| **Version** | 1.0.0-DRAFT |
| **Lifecycle Status** | `PENDING_APPROVAL` |
| **Author** | Senior Business Analyst & E-Commerce Requirements Engineering Team |

---

## 1. Uncertainty Governance Framework

To maintain architectural integrity and prevent undocumented assumptions from silently becoming technical debt, this document maintains three dedicated operational registers:
1. **Assumptions Register (`ASM-xxx`):** Unconfirmed premises adopted to enable requirements documentation progress.
2. **Dependencies Register (`DEP-xxx`):** External commercial, regulatory, or technical prerequisites required for business execution.
3. **Open Questions Register (`OQ-xxx`):** Unresolved commercial policy choices requiring formal Business Owner decision and sign-off.

---

## 2. Assumptions Register

| Assumption ID | Baseline Assumption | Commercial Reason | Impact if Assumption is Incorrect | Validation Method | Decision Owner | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| <a id="asm-001"></a>**ASM-001** | Primary operational country is India. | Domestic retail electronics market context and legal framework. | Requires re-architecting cross-border customs, multi-tax calculations, and international shipping. | Formal confirmation by Enterprise Business Owner. | Business Owner | `PROPOSED` |
| <a id="asm-002"></a>**ASM-002** | Transaction currency is Indian Rupee (INR - ₹) exclusively for Phase 1. | Eliminates currency conversion risk and multi-currency accounting complexity at launch. | Requires multi-currency pricing, exchange rate feeds, and foreign exchange bank reconciliation. | Commercial policy sign-off. | Business Owner / Finance | `PROPOSED` |
| <a id="asm-003"></a>**ASM-003** | Retail display prices are tax-inclusive (MRP / Gross Selling Price). | Compliance with Indian Consumer Protection (E-Commerce) Rules and consumer price transparency. | Requires re-engineering cart calculations and checkout UI if taxes are added on top at checkout. | Legal / Tax Advisor review. | Finance Lead | `PROPOSED` |
| <a id="asm-004"></a>**ASM-004** | The enterprise operates from a single primary central fulfillment warehouse at launch. | Simplifies initial inventory allocation and warehouse logistics for initial launch. | Multi-warehouse order routing, split shipments, and regional stock balancing must be engineered earlier. | Warehouse operations audit. | Operations Lead | `PROPOSED` |
| <a id="asm-005"></a>**ASM-005** | Pre-orders and backorders are out of scope for the baseline release. | Prevents operational liability for out-of-stock electronics with unpredictable supply chain dates. | Requires engineering backorder queues, delayed capture, and customer cancellation waivers. | Commercial merchandising policy review. | Business Owner | `CONFIRMED` |
| <a id="asm-006"></a>**ASM-006** | Digital customer identity is primarily tied to mobile phone numbers. | Reflects Indian consumer shopping patterns where mobile OTP is the primary authentication rail. | If email-first is mandated, registration funnels and communications channels must be redesigned. | Customer UX persona review. | Product Lead | `PROPOSED` |
| <a id="asm-007"></a>**ASM-007** | A standard 7-calendar-day return window applies to consumer electronics. | Industry-standard duration balancing customer satisfaction with prevention of device wear and fraud. | Financial return rate projections and reverse logistics contracts will require renegotiation. | Business Owner policy sign-off (subject to [OQ-008](#oq-008)). | Business Owner | `PROPOSED` |
| <a id="asm-008"></a>**ASM-008** | External payment gateway will handle all cardholder credential entry (Hosted / Iframe). | Completely isolates the Chellaa platform from PCI-DSS Level 1 audit liability and card security scope. | Enterprise must undergo full PCI-DSS Level 1 certification, dramatically increasing security costs. | Security & Gateway contract review. | Security Lead | `CONFIRMED` |
| <a id="asm-009"></a>**ASM-009** | Third-party courier partners will provide REST APIs or Webhooks for automated AWB and tracking. | Eliminates manual waybill typing and manual customer order tracking phone inquiries. | Warehouse operators will be forced to manually enter tracking numbers into spreadsheet batches. | Logistics provider RFP evaluation. | Logistics Lead | `CONFIRMED` |
| <a id="asm-010"></a>**ASM-010** | Cash on Delivery (COD) will be restricted to orders under ₹10,000 and verified PIN codes. | High-value electronics (smartphones, laptops) carry extreme RTO theft and cash handling risk under COD. | Uncapped COD risks massive return-to-origin freight costs and cash collection shrinkage. | Finance & Risk assessment. | Finance Lead | `PROPOSED` |

---

## 3. Dependencies Register

| Dependency ID | Dependency Name | Type | Description | Affected Requirements | Potential Commercial Risk | Mitigation / Resolution Strategy | Owner | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| <a id="dep-001"></a>**DEP-001** | Razorpay Merchant Agreement | Commercial Integration | Formal Razorpay merchant account agreement, production credentials, settlement terms, refund configuration, and webhook setup. | `FEAT-PAY-001`, `FR-PAY-001` | Inability to accept online payments or execute automated refunds at launch. | Initiate provider RFP immediately; evaluate sandbox APIs in parallel. | Finance Lead | `OPEN` |
| <a id="dep-002"></a>**DEP-002** | Shiprocket Logistics Partner Agreement | Operational Integration | Shiprocket account/contract, API credentials, serviceability validation, carrier availability, COD remittance terms, reverse pickup, and agreed service levels. | `FEAT-SHP-001`, `FR-SHP-001` | Parcels cannot be automatically booked or tracked via API; manual shipping delays. | Shortlist top 2 logistics partners; finalize SLA on API uptime and COD remittance. | Operations Lead | `OPEN` |
| <a id="dep-003"></a>**DEP-003** | Telecom SMS DLT Registration & WhatsApp Business API | Regulatory Integration | Distributed Ledger Technology (DLT) template approval for transactional SMS and Meta WhatsApp Business API account. | `FEAT-NTF-001`, `FR-NTF-001` | Transactional OTPs and shipping alerts blocked by telecom operators as spam. | Register enterprise entity and SMS templates on commercial telecom DLT portal immediately. | Operations Lead | `OPEN` |
| <a id="dep-004"></a>**DEP-004** | Enterprise GSTIN & Invoicing Numbering Scheme | Legal & Tax | Registered corporate Goods and Services Tax Identification Number (GSTIN) and official tax invoice format. | `FEAT-FIN-001`, `FR-FIN-001` | Generated tax invoices non-compliant with statutory tax audits; regulatory penalties. | Obtain legal corporate tax opinion on HSN rate codes and sequential invoice numbering syntax. | Finance Lead | `OPEN` |
| <a id="dep-005"></a>**DEP-005** | Master Electronics Catalog & Brand Specifications | Business Merchandising | High-resolution photography, accurate technical specification sheets, and manufacturer warranty terms. | `FEAT-CAT-001`, `FR-CAT-001` | Inaccurate product descriptions leading to customer disputes and elevated return rates. | Establish structured ingestion spreadsheet templates for supplier distributors. | Merchandising Lead | `OPEN` |
| <a id="dep-006"></a>**DEP-006** | Physical Warehouse Infrastructure & Barcode Hardware | Physical Operations | Physical fulfillment center equipped with labeled bin locations, packaging benches, and handheld barcode scanners. | `FEAT-INV-001`, `FR-INV-001` | Software inventory tracking disconnected from physical reality; picking errors. | Define standardized SKU barcode symbologies (Code 128 / EAN-13) before inwarding stock. | Warehouse Lead | `OPEN` |

---

## 4. Open Questions Register

The following 22 strategic commercial decisions must be formally reviewed and resolved by the Enterprise Business Owner and relevant domain leads before finalizing the technical architecture baseline.

---

<a id="oq-001"></a>
### OQ-001: Operating Country & Domestic vs Cross-Border Scope
* **Question:** Which country or countries will the e-commerce business operate in at launch?
* **Why the Decision Matters:** Defines legal jurisdiction, statutory tax calculation engines (GST vs VAT vs US Sales Tax), currency, courier partner integrations, and consumer protection regulations.
* **Affected Domains:** Business Scope, Pricing, Tax & Invoicing, Shipping, Legal Compliance.
* **Affected Requirement IDs:** `FEAT-FIN-001`, `FEAT-SHP-001`, `FR-FIN-001`.
* **Possible Options:**
  * *Option A:* Domestic India only at launch.
  * *Option B:* India primary, with cross-border shipping to neighboring regions (e.g., Middle East / South Asia).
  * *Option C:* Multi-national global retail launch.
* **Recommended Option:** **Option A (Domestic India only at launch).** Allows the enterprise to master localized operations, national tax compliance (GST), and domestic logistics before taking on customs and currency risks.
* **Impact of Delaying:** Blocks finalizing the tax calculation engine and courier serviceability architecture.
* **Priority:** `P0` (Critical)
* **Decision Owner:** Enterprise Business Owner.
* **Business Owner Decision:** **Option A — Domestic India only at launch.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-002"></a>
### OQ-002: Currencies and Language Support
* **Question:** Which transaction currencies and languages must be supported on customer and admin interfaces?
* **Why the Decision Matters:** Multi-currency impacts database schemas, payment gateway selection, and price change volatility. Multi-language impacts content management, product descriptions, and localized notification templates.
* **Affected Domains:** Customer Experience, Catalog, Payments, Notifications.
* **Affected Requirement IDs:** `FEAT-CAT-001`, `FEAT-PAY-001`, `NFR-LOC-001`.
* **Possible Options:**
  * *Option A:* Single currency (INR - ₹) and single language (English) for Phase 1.
  * *Option B:* Single currency (INR), multi-language (English, Hindi, Tamil, Telugu).
  * *Option C:* Multi-currency (INR, USD, AED, EUR) with live exchange rate recalculation.
* **Recommended Option:** **Option A (INR and English only for launch baseline).** Regional language localization and multi-currency should be deferred to Phase 3.
* **Impact of Delaying:** Minor impact on backend requirements; major impact on frontend localization frameworks.
* **Priority:** `P1`
* **Decision Owner:** Enterprise Business Owner / Marketing Lead.
* **Business Owner Decision:** **Option A — INR (₹) and English only.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-003"></a>
### OQ-003: Required Customer Payment Methods
* **Question:** Which customer payment methods are mandatory for the production launch?
* **Why the Decision Matters:** Dictates payment gateway configuration, checkout UI options, failure handling, and reconciliation procedures.
* **Affected Domains:** Checkout, Payments, Financial Reconciliation.
* **Affected Requirement IDs:** `FEAT-PAY-001`, `FR-PAY-001`, `BR-PAY-001`.
* **Possible Options:**
  * *Option A:* UPI (Intent + QR), Credit Cards, Debit Cards, Net Banking.
  * *Option B:* Option A plus Buy-Now-Pay-Later (BNPL) and Card EMI options.
  * *Option C:* Option A plus Cash on Delivery (COD).
* **Recommended Option:** **Option B (UPI, Cards, Net Banking, and Bank EMI).** High-value electronics heavily rely on EMI and UPI in the Indian consumer retail landscape.
* **Business Owner Decision:** **Option C — Option A payment methods plus Cash on Delivery (COD).** Recorded from the explicit Business Owner selection on `2026-10-24`.
* **COD Policy Dependency:** COD must follow the already recorded `OQ-006` / `ADQ-003` policy: restricted COD up to ₹10,000, delivery OTP, and ₹50 non-refundable convenience fee.
* **Impact of Delaying:** Influences payment gateway evaluation and checkout orchestration design.
* **Priority:** `P0`
* **Decision Owner:** EZHIL SELVAN P. — Founder; Finance Lead review remains required for implementation/payment-provider implications.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-004"></a>
### OQ-004: Payment Gateway Partner Selection
* **Question:** Which external payment gateway provider will be integrated for online payments at launch?
* **Why the Decision Matters:** Determines the payment API/SDK, transaction fees, webhook verification, payment status handling, refund API integration, settlement reconciliation, and merchant onboarding dependency.
* **Affected Domains:** Payments, External Integrations.
* **Affected Requirement IDs:** `FEAT-PAY-001`, `FEAT-INT-001`.
* **Possible Options:**
  * *Option A:* Single payment gateway — select Razorpay or Cashfree.
  * *Option B:* Dual gateway architecture — primary Razorpay with a secondary failover provider.
* **Business Owner Decision:** **Single gateway — Razorpay only.** Although the earlier option label mentioned Option B, “Razorpay only” means no secondary gateway at launch; this is recorded as the single-gateway approach (Option A), with Razorpay as the named provider. Recorded 2026-10-24 from the explicit Business Owner instruction.
* **Implementation Constraints:** Integrate Razorpay hosted/checkout flow; verify webhook signatures; process payment events idempotently; reconcile settlements; support source refunds; do not store raw card PAN/CVV. Do not automatically retry an ambiguous/pending payment through another provider.
* **Impact of Delaying:** Blocks final provider-specific integration configuration and merchant onboarding, but does not itself approve the architecture.
* **Priority:** `P1`
* **Decision Owner:** EZHIL SELVAN P. — Founder; Finance and Technical review remain required.
* **Status:** `CONFIRMED` (Business Owner selection recorded; merchant agreement and technical validation remain open).

---

<a id="oq-005"></a>
### OQ-005: Courier and Shipping Logistics Partner
* **Question:** Which courier/logistics integration approach will be used for dispatch, tracking, and reverse pickup at launch?
* **Why the Decision Matters:** Influences delivery PIN-code coverage, shipping label generation, tracking webhooks, COD remittance support, and reverse pickup reliability.
* **Affected Domains:** Shipping & Logistics, Returns, Reverse Logistics.
* **Affected Requirement IDs:** `FEAT-SHP-001`, `FR-SHP-001`.
* **Possible Options:**
  * *Option A:* Direct integration with one national carrier.
  * *Option B:* Multi-carrier logistics aggregator such as Shiprocket.
  * *Option C:* Direct API integrations with two national carriers.
* **Business Owner Decision:** **Option B — Shiprocket multi-carrier aggregator.** Recorded 2026-10-24 from the Business Owner's selection of Option B for the logistics decision.
* **Implementation Constraints:** Validate serviceability by PIN code, shipment creation, AWB/label generation, pickup booking, tracking webhooks/polling, cancellations, reverse pickup, COD support/remittance, API rate limits, and failure handling before production launch.
* **Impact of Delaying:** Blocks final provider contract/SLA and production credentials; does not itself approve the architecture.
* **Priority:** `P1`
* **Decision Owner:** EZHIL SELVAN P. — Founder; Operations and Technical review remain required.
* **Status:** `CONFIRMED` (Business Owner selection recorded; partner agreement and integration validation remain open).

---

<a id="oq-006"></a>
### OQ-006: Cash on Delivery (COD) Policy
* **Question:** Will Cash on Delivery (COD) be offered to customers at launch, and if so, under what commercial safeguards?
* **Why the Decision Matters:** COD in consumer electronics experiences high Return-to-Origin (RTO) rates ($> 20\%$), carrier cash handling fees, courier remittance delays, and delivery boy theft risks.
* **Affected Domains:** Checkout, Payments, Shipping, Order Management.
* **Affected Requirement IDs:** `FEAT-CHK-001`, `FEAT-PAY-001`, `BR-ORD-003`.
* **Possible Options:**
  * *Option A:* 100% Prepaid only at launch (Zero COD).
  * *Option B:* Restricted COD: Available only for order values $\le ₹10,000$ and verified non-high-risk PIN codes, with a nominal ₹50 COD convenience fee.
  * *Option C:* Unrestricted COD across all orders.
* **Recommended Option:** **Option B (Restricted COD with ₹10,000 ceiling and OTP verification at delivery).** Balances consumer trust with enterprise risk mitigation.
* **Impact of Delaying:** Direct impact on checkout payment validation rules and cash collection reconciliation workflows.
* **Priority:** `P0`
* **Decision Owner:** Enterprise Business Owner / Finance Lead.
* **Business Owner Decision:** **Option B — Restricted COD up to ₹10,000, delivery OTP, and ₹50 non-refundable convenience fee.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-007"></a>
### OQ-007: Warehouse Topology at Launch
* **Question:** Will the enterprise operate from a single central warehouse or multiple regional warehouses at launch?
* **Why the Decision Matters:** Multiple warehouses require stock allocation algorithms, regional inventory partitioning, split shipments, and complex tax invoicing across multiple state GSTINs.
* **Affected Domains:** Inventory Management, Order Routing, Tax Invoicing.
* **Affected Requirement IDs:** `FEAT-INV-001`, `FEAT-FIN-001`, `FR-INV-001`.
* **Possible Options:**
  * *Option A:* Single central fulfillment warehouse at launch; multi-warehouse capable data model.
  * *Option B:* Multi-warehouse operations from Day 1 (e.g., North and South hubs).
* **Recommended Option:** **Option A (Single Central Warehouse at launch).** Keeps physical inventory operations lean while ensuring software schemas support adding warehouse locations later without schema rewrites.
* **Impact of Delaying:** Affects order routing logic and inventory reservation scopes.
* **Priority:** `P1`
* **Decision Owner:** Operations Lead.
* **Status:** `UNDER_REVIEW` (Proposed: Option A).

---

<a id="oq-008"></a>
### OQ-008: Customer Returns and Inspection Policy
* **Question:** What is the precise return and replacement policy for consumer electronics (policy window, restocking fee, return reasons)?
* **Why the Decision Matters:** Electronics suffer high depreciation upon box seal opening. Generous return policies attract return fraud; strict policies deter genuine buyers.
* **Affected Domains:** Returns & Refunds, Customer Support, Reverse Logistics.
* **Affected Requirement IDs:** `FEAT-RET-001`, `FR-RET-001`, `BR-RET-001`, `BR-RET-002`.
* **Possible Options:**
  * *Option A:* 7-day "Replacement Only" for verified hardware defects; zero returns for "buyer's remorse" / change of mind.
  * *Option B:* 7-day Return for Refund or Replacement, provided box seal is intact or technician verifies defect.
  * *Option C:* 14-day open return policy.
* **Recommended Option:** **Option B (7-day window; mandatory defect verification or sealed box check).** Aligns with leading Indian electronics retailers (Croma, Reliance Digital).
* **Impact of Delaying:** Dictates warehouse return inspection grading criteria and customer self-service return workflows.
* **Priority:** `P0`
* **Decision Owner:** Enterprise Business Owner.
* **Business Owner Decision:** **Option B — 7-day return/refund or replacement, subject to technician diagnostic inspection and serial/IMEI verification.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-009"></a>
### OQ-009: Tax Structure and Retail Pricing Conventions
* **Question:** What tax regime governs the enterprise, and are storefront prices tax-inclusive or tax-exclusive?
* **Why the Decision Matters:** Determines tax calculation formula, invoice layout, and price presentation in catalogs and carts.
* **Affected Domains:** Pricing, Tax, Invoicing, Cart.
* **Affected Requirement IDs:** `FEAT-PRC-001`, `FEAT-FIN-001`, `BR-FIN-001`.
* **Possible Options:**
  * *Option A:* Indian GST (CGST/SGST for intra-state, IGST for inter-state); prices displayed are 100% Tax-Inclusive.
  * *Option B:* Tax-Exclusive pricing (tax calculated and added at checkout).
* **Recommended Option:** **Option A (Indian GST; 100% Tax-Inclusive display prices).** Mandated by Indian consumer protection guidelines.
* **Impact of Delaying:** High impact on cart and checkout price calculation formulas.
* **Priority:** `P0`
* **Decision Owner:** Finance Lead / Tax Advisor.
* **Business Owner Decision:** **Option A — GST-inclusive storefront pricing with tax breakdown/invoice reverse extraction.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-010"></a>
### OQ-010: Customer Account Verification Channels
* **Question:** Is customer registration validated via SMS OTP, Email verification link, or both?
* **Why the Decision Matters:** Directly impacts onboarding friction, drop-off rates, and telecom SMS gateway operational costs.
* **Affected Domains:** Customer Accounts, Authentication, Notifications.
* **Affected Requirement IDs:** `FEAT-CUST-001`, `FR-CUST-001`.
* **Possible Options:**
  * *Option A:* Mobile number mandatory with SMS OTP verification; email optional.
  * *Option B:* Email mandatory with verification link; mobile optional.
  * *Option C:* Both mobile (SMS OTP) and email mandatory.
* **Recommended Option:** **Option A (Mobile-first with SMS OTP; optional email).** Yields lowest signup friction and captures verified mobile numbers needed for courier delivery OTPs.
* **Impact of Delaying:** Determines registration data schema and identity provider authentication flow.
* **Priority:** `P0`
* **Decision Owner:** Product Lead / Business Owner.
* **Business Owner Decision:** **Option A — Mobile-first SMS OTP; email optional.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-011"></a>
### OQ-011: Guest Checkout Support
* **Question:** Should the platform allow guest shoppers to complete purchases without creating an account?
* **Why the Decision Matters:** Guest checkout reduces checkout abandonment, but complicates order tracking, repeat purchases, return management, and customer lifetime value (LTV) analytics.
* **Affected Domains:** Shopping Cart, Checkout, Order Management, Customer Accounts.
* **Affected Requirement IDs:** `FEAT-CHK-001`, `ACT-010`.
* **Possible Options:**
  * *Option A:* Mandatory account registration before checkout.
  * *Option B:* Guest checkout permitted, with automated account creation using the checkout mobile number.
  * *Option C:* Pure anonymous guest checkout without account creation.
* **Recommended Option:** **Option B (Streamlined Guest Checkout with seamless automatic account creation via phone OTP).** Gives the customer a frictionless guest experience while provisioning an account in the background.
* **Impact of Delaying:** Affects checkout state machine and user session boundary designs.
* **Priority:** `P1`
* **Business Owner Decision:** **Option B — Guest checkout permitted, with automated account creation using the checkout mobile number and phone OTP verification.**
* **Decision Owner:** EZHIL SELVAN P. — Founder (Business Owner selection recorded 2026-10-24).
* **Status:** `CONFIRMED` (Business Owner selected Option B; Phase 1 baseline approval remains pending).

---

<a id="oq-012"></a>
### OQ-012: Customer Reviews and Ratings Scope
* **Question:** Are customer ratings and text reviews required in Phase 1, and what moderation rules apply?
* **Why the Decision Matters:** Reviews require verified purchase checks, profanity filters, image upload moderation, and review aggregation calculation jobs.
* **Affected Domains:** Product Details, Reviews, Moderation.
* **Affected Requirement IDs:** `FEAT-REV-001`, `FR-REV-001`.
* **Possible Options:**
  * *Option A:* Out of scope for Phase 1 baseline; deferred to Phase 2.
  * *Option B:* In scope for Phase 1: Verified buyers only, with mandatory admin moderation before publication.
  * *Option C:* Open unmoderated reviews.
* **Recommended Option:** **Option A (Deferred to Phase 2).** Focus initial baseline on core transaction reliability; integrate reviews in the frontend release.
* **Impact of Delaying:** Removes review service scope from Phase 1.
* **Priority:** `P2`
* **Decision Owner:** Product Lead.
* **Status:** `UNDER_REVIEW` (Proposed: Option A).

---

<a id="oq-013"></a>
### OQ-013: Loyalty Points, Store Credit & Referral Programs
* **Question:** Are loyalty rewards, wallet store credits, or customer referral schemes in scope for initial launch?
* **Why the Decision Matters:** Introduces complex financial ledger tracking, point expiration rules, tax accounting adjustments, and referral fraud screening.
* **Affected Domains:** Customer Accounts, Promotions, Financial Ledger.
* **Affected Requirement IDs:** `FEAT-ADV-001`.
* **Possible Options:**
  * *Option A:* Explicitly Out of Scope for launch; deferred to post-launch roadmap.
  * *Option B:* Basic store credit wallet for refund processing only.
  * *Option C:* Full tiered loyalty rewards program at launch.
* **Recommended Option:** **Option A (Out of Scope for initial baseline).** Keep financial workflows clean and direct refunds to source instruments.
* **Impact of Delaying:** None; reduces baseline scope complexity.
* **Priority:** `P2`
* **Decision Owner:** Business Owner / Marketing Lead.
* **Status:** `CONFIRMED` (Option A - Out of Scope for Phase 1).

---

<a id="oq-014"></a>
### OQ-014: Pre-orders and Backorders Policy
* **Question:** Can a customer place an order for out-of-stock or unreleased products (pre-orders)?
* **Why the Decision Matters:** Pre-orders require delayed payment capture, inventory allocation queues, supplier SLA tracking, and cancellation guarantees.
* **Affected Domains:** Inventory Management, Checkout, Order Management.
* **Affected Requirement IDs:** `FEAT-INV-001`, `BR-INV-001`.
* **Possible Options:**
  * *Option A:* Strictly prohibited at launch ($\text{Stock} = 0$ disables purchasing).
  * *Option B:* Permitted for designated flagship products with confirmed distributor delivery dates.
* **Recommended Option:** **Option A (Strictly prohibited at launch).** Re-evaluate only after enterprise warehouse operations mature.
* **Impact of Delaying:** Confirms non-negative inventory rule `BR-INV-001`.
* **Priority:** `P1`
* **Decision Owner:** Business Owner / Operations Lead.
* **Status:** `CONFIRMED` (Option A - Out of Scope for Phase 1).

---

<a id="oq-015"></a>
### OQ-015: Mandatory Notification Channels
* **Question:** Which communication channels are mandatory for transactional alerts versus promotional campaigns?
* **Why the Decision Matters:** Dictates integration providers, template approvals, compliance consent tracking, and ongoing messaging budget.
* **Affected Domains:** Notifications, Customer Privacy.
* **Affected Requirement IDs:** `FEAT-NTF-001`, `FR-NTF-001`, `BR-NTF-001`.
* **Possible Options:**
  * *Option A:* SMS (for OTPs) and Email (for Invoices and Orders) only.
  * *Option B:* SMS, Email, and WhatsApp Business API for all order lifecycle events.
  * *Option C:* In-app push notifications only.
* **Recommended Option:** **Option B (SMS for security OTPs; Email for legal tax invoices; WhatsApp for customer order tracking updates).** WhatsApp delivers the highest engagement and read rates for Indian consumers.
* **Impact of Delaying:** Determines notification provider selection during Architecture Phase.
* **Priority:** `P1`
* **Decision Owner:** Marketing Lead / Operations Lead.
* **Status:** `UNDER_REVIEW` (Proposed: Option B).

---

<a id="oq-016"></a>
### OQ-016: Expected Peak Traffic and Order Volumes
* **Question:** What are the projected baseline and peak promotional transaction volumes (orders per minute, daily active users)?
* **Why the Decision Matters:** Directly drives database sizing, caching tier design, Kafka partition counts, and Kubernetes cluster resource allocation.
* **Affected Domains:** Non-Functional Requirements, Capacity Planning.
* **Affected Requirement IDs:** `NFR-SCL-001`, `NFR-PERF-001`.
* **Possible Options:**
  * *Option A:* Modest launch: 20 orders/min baseline; 200 orders/min peak.
  * *Option B:* High-volume launch: 100 orders/min baseline; 1,000 orders/min peak.
  * *Option C:* Enterprise scale: 500 orders/min baseline; 5,000 orders/min peak.
* **Recommended Option:** **Option B (100 OPM baseline, 1,000 OPM peak).** Balances infrastructure cost efficiency with headroom for aggressive festive promotional campaigns.
* **Impact of Delaying:** Affects technical infrastructure sizing and stress-testing specifications.
* **Priority:** `P0`
* **Decision Owner:** Enterprise Business Owner / Technical Lead.
* **Business Owner Decision:** **Option B — 100 orders/minute sustained; 1,000 orders/minute festive peak.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-017"></a>
### OQ-017: Production Availability and Recovery Targets (RTO / RPO)
* **Question:** What are the contractual or business owner targets for platform uptime, Recovery Point Objective (RPO), and Recovery Time Objective (RTO)?
* **Why the Decision Matters:** Higher availability (e.g., 99.99% vs 99.9%) and near-zero RTO require active-active multi-region cloud deployment, doubling infrastructure budgets.
* **Affected Domains:** Business Continuity, Infrastructure, SRE.
* **Affected Requirement IDs:** `NFR-AVL-001`, `NFR-BCM-001`.
* **Possible Options:**
  * *Option A:* 99.9% Uptime, $\text{RPO} \le 5\text{ minutes}$, $\text{RTO} \le 2\text{ hours}$ (Single-region multi-AZ deployment with cold standby).
  * *Option B:* 99.99% Uptime, $\text{RPO} = 0$, $\text{RTO} \le 15\text{ minutes}$ (Multi-region active-active deployment).
* **Recommended Option:** **Option A (99.9% Uptime, RPO $\le 5$ mins, RTO $\le 2$ hours).** Highly cost-effective for single-retailer launch while providing robust disaster recovery protection.
* **Impact of Delaying:** Sets parameters for cloud architecture design.
* **Priority:** `P0`
* **Decision Owner:** Enterprise Business Owner.
* **Business Owner Decision:** **Option A — 99.9% uptime, RPO ≤ 5 minutes, RTO ≤ 2 hours.** Selection recorded 2026-10-24 in the Executive Decision Register.
* **Decision Owner:** EZHIL SELVAN P. — Founder.
* **Status:** `CONFIRMED` (Business Owner selection recorded; Phase 1 baseline approval remains pending).

---

<a id="oq-018"></a>
### OQ-018: Essential Executive Business Reports
* **Question:** Which financial, inventory, and customer reports must be accessible to the Business Owner at launch?
* **Why the Decision Matters:** Influences reporting read-model design, data warehouse ETL pipelines, and export formats.
* **Affected Domains:** Analytics & Reporting, Finance.
* **Affected Requirement IDs:** `FEAT-RPT-001`, `FR-RPT-001`.
* **Possible Options:**
  * *Option A:* Standard daily operational reports: Daily Sales (GMV/Net), Product Velocity, Inventory Low-Stock, and GST Tax Liability.
  * *Option B:* Option A plus real-time BI dashboards with predictive replenishment and customer cohort retention analysis.
* **Recommended Option:** **Option A (Standard core operational and financial reports).** Advanced machine-learning BI can be layered on in subsequent phases.
* **Impact of Delaying:** Defines reporting schema requirements.
* **Priority:** `P1`
* **Decision Owner:** Business Owner / Finance Lead.
* **Status:** `UNDER_REVIEW` (Proposed: Option A).

---

<a id="oq-019"></a>
### OQ-019: Legacy Historical Data Migration Scope
* **Question:** Does historical customer, product, or order data exist in legacy spreadsheets or prior systems that must be migrated into the new platform?
* **Why the Decision Matters:** Legacy data migration requires writing ETL mapping scripts, cleansing duplicate records, and reconciling historical financial balances.
* **Affected Domains:** Data Migration, Catalog, Customer Accounts.
* **Affected Requirement IDs:** `FEAT-DAT-001`.
* **Possible Options:**
  * *Option A:* Clean greenfield launch (only master product catalog and active warehouse stock imported via bulk spreadsheet).
  * *Option B:* Full historical order and customer account migration from legacy systems.
* **Recommended Option:** **Option A (Greenfield launch with master catalog and inventory import).** Avoids contaminating the new architecture with incomplete or non-compliant legacy order data.
* **Impact of Delaying:** Determines whether historical migration pipelines are in baseline scope.
* **Priority:** `P1`
* **Decision Owner:** Enterprise Business Owner.
* **Status:** `UNDER_REVIEW` (Proposed: Option A).

---

<a id="oq-020"></a>
### OQ-020: Data Retention and Privacy Governance (DPDPA)
* **Question:** What data retention policies and user privacy rights must be implemented to comply with national privacy laws?
* **Why the Decision Matters:** Balancing customer "Right to be Forgotten" with statutory requirements to retain financial tax records for 8 years.
* **Affected Domains:** Data Privacy, Security, Auditing.
* **Affected Requirement IDs:** `NFR-SEC-003`, `BR-DAT-001`.
* **Possible Options:**
  * *Option A:* Retain financial tax documents (invoices, credit notes) for 8 years; allow customer profile anonymization and marketing consent withdrawal.
  * *Option B:* Complete immediate purging of all records upon customer request.
* **Recommended Option:** **Option A (Preserve financial records in locked archive; anonymize marketing and profile PII).** Mandated by statutory tax auditing laws.
* **Impact of Delaying:** Dictates data purge job logic and user anonymization schemas.
* **Priority:** `P0`
* **Decision Owner:** Legal Counsel / Business Owner.
* **Status:** `CONFIRMED` (Option A).

---

<a id="oq-021"></a>
### OQ-021: Software Release and Deployment Approval Governance
* **Question:** What governance process is required before deploying software releases or configuration updates to production?
* **Why the Decision Matters:** Determines deployment gating, manual approval sign-offs, and staging verification protocols.
* **Affected Domains:** Release Management, CI/CD, Governance.
* **Affected Requirement IDs:** `FEAT-REL-001`, `NFR-REL-001`.
* **Possible Options:**
  * *Option A:* Fully automated continuous deployment (CD) on passing automated tests.
  * *Option B:* Automated CI pipeline with mandatory manual business approval sign-off before production release execution.
* **Recommended Option:** **Option B (Automated CI with mandatory manual release approval sign-off).** Guarantees business leadership maintains total control over production modifications.
* **Impact of Delaying:** Shapes CI/CD pipeline gating design.
* **Priority:** `P1`
* **Decision Owner:** Technical Lead / Business Owner.
* **Status:** `CONFIRMED` (Option B).

---

<a id="oq-022"></a>
### OQ-022: Operational Monitoring and Incident Escalation Protocols
* **Question:** What operational monitoring thresholds and incident escalation procedures should be established for business-critical failures?
* **Why the Decision Matters:** Governs SRE on-call rotations, automated alert routing, and incident response runbooks.
* **Affected Domains:** Observability, Operations, Business Continuity.
* **Affected Requirement IDs:** `FEAT-OPS-001`, `NFR-OBS-002`.
* **Possible Options:**
  * *Option A:* Automated alerts (Slack/Email/SMS) for high error rates ($> 2\%$) and payment gateway dropouts, escalating to designated technical leads.
  * *Option B:* 24/7 dedicated third-party Network Operations Center (NOC).
* **Recommended Option:** **Option A (Automated alerts to internal engineering/operations team).** Cost-effective and highly responsive for enterprise operational scale.
* **Impact of Delaying:** Sets alert notification routing during operational deployment.
* **Priority:** `P1`
* **Decision Owner:** Technical Operations Lead.
* **Status:** `CONFIRMED` (Option A).

---

## 5. Summary Status of Open Questions

| Question ID | Decision Topic | Priority | Current Status | Recommended Baseline Parameter |
| :---: | :--- | :---: | :---: | :--- |
| **OQ-001** | Operating Country Scope | `P0` | `CONFIRMED` | Owner-selected Option A: Domestic India only at launch |
| **OQ-002** | Currencies & Languages | `P1` | `CONFIRMED` | Owner-selected Option A: INR (₹) and English only |
| **OQ-003** | Mandatory Payment Methods | `P0` | `CONFIRMED` | Owner-selected Option C: UPI, cards, net banking, plus COD under OQ-006 policy |
| **OQ-004** | Payment Gateway Selection | `P1` | `OPEN` | Dual Gateway (Primary + Failover) |
| **OQ-005** | Courier Logistics Selection | `P1` | `OPEN` | Multi-carrier aggregator (Shiprocket/similar) |
| **OQ-006** | Cash on Delivery (COD) Policy | `P0` | `CONFIRMED` | Owner-selected Option B: ₹10,000 cap, delivery OTP, ₹50 fee |
| **OQ-007** | Warehouse Launch Topology | `P1` | `UNDER_REVIEW` | Single central warehouse at launch |
| **OQ-008** | Returns & Inspection Policy | `P0` | `CONFIRMED` | Owner-selected Option B: 7-day return/refund with diagnostic gate |
| **OQ-009** | Tax Structure & Display Prices | `P0` | `CONFIRMED` | Owner-selected Option A: GST-inclusive display |
| **OQ-010** | Customer Verification Method | `P0` | `CONFIRMED` | Owner-selected Option A: mobile SMS OTP; email optional |
| **OQ-011** | Guest Checkout Support | `P1` | `CONFIRMED` | Owner-selected Option B: guest checkout with phone-OTP auto-account |
| **OQ-012** | Customer Reviews & Ratings | `P2` | `UNDER_REVIEW` | Deferred to Phase 2 (Frontend) |
| **OQ-013** | Loyalty & Store Credit | `P2` | `CONFIRMED` | Out of scope for Phase 1 baseline |
| **OQ-014** | Pre-orders & Backorders | `P1` | `CONFIRMED` | Out of scope (Strict non-negative stock) |
| **OQ-015** | Notification Channels | `P1` | `UNDER_REVIEW` | SMS (OTP), Email (Invoice), WhatsApp (Order) |
| **OQ-016** | Peak Traffic & Order Capacity | `P0` | `CONFIRMED` | Owner-selected Option B: 100 OPM sustained; 1,000 OPM peak |
| **OQ-017** | Availability, RTO & RPO Targets | `P0` | `CONFIRMED` | Owner-selected Option A: 99.9% uptime; RPO ≤ 5m; RTO ≤ 2h |
| **OQ-018** | Required Business Reports | `P1` | `UNDER_REVIEW` | Standard daily sales, velocity, GST reports |
| **OQ-019** | Legacy Data Migration Scope | `P1` | `UNDER_REVIEW` | Greenfield launch with catalog import |
| **OQ-020** | Data Retention & Privacy (DPDPA)| `P0` | `CONFIRMED` | 8-year tax retention; anonymize profile PII |
| **OQ-021** | Release Approval Governance | `P1` | `CONFIRMED` | Automated CI with manual release gate |
| **OQ-022** | Operational Incident Protocols | `P1` | `CONFIRMED` | Automated Slack/SMS alerts for $> 2\%$ error |
