# Enterprise Business Owner Strategic Decision Register

| Document Attribute | Details |
| :--- | :--- |
| **Document ID** | `EXEC-DEC-REG-01` |
| **Document Title** | Enterprise Business Owner Strategic Decision Register & Policy Ratification Record |
| **Project Name** | Chellaa E-Commerce Platform |
| **Target Repository** | [`ezhilselvan1109/chellaa-system`](https://github.com/ezhilselvan1109/chellaa-system) |
| **Target Branch** | `dev` |
| **File Location** | `docs/00-requirements/review/executive-decision-register.md` |
| **Creation Date** | 2026-10-23 |
| **Author Roles** | Senior Business Analyst, Solution Architect & Architecture Governance Lead |
| **Target Reviewer** | Enterprise Business Owner |
| **Governance Status** | **`OWNER_DECISIONS_RECORDED_PENDING_PHASE1_APPROVAL`** (Business Owner selections recorded; baseline approval remains pending) |
| **Phase 1 Baseline Status** | **`PENDING_APPROVAL`** (Maintained strictly per governance constraints) |

---

## 1. Purpose and Governance Instructions

This decision register is prepared specifically for the **Enterprise Business Owner**. It consolidates the recorded commercial, regulatory, and operational decision history (ADQ-001 through ADQ-015, including explicitly superseded historical decisions) that govern the Phase 1 Business Requirements Document (BRD) baseline and define mandatory boundaries for subsequent Phase 2 Technical Architecture design.

### Mandatory Governance Rules Observed:
1. **Non-Binding Proposals:** All proposals presented by the engineering team are explicitly designated as **`NON-BINDING RECOMMENDATIONS`**. Engineering has provided technical analysis and operational recommendations, but engineering **cannot** decide commercial policy on behalf of the business.
2. **No Inferred Selections or Fabricated Records:** AI agents and technical leads must not infer business selections or invent reviewer details, dates, signatures, or authorization hashes. Explicit selections and dates supplied by the authorized Business Owner may be transcribed accurately.
3. **Formal Resolution Required:** Each decision must be reviewed, an option selected, and commercial conditions documented by the authorized Enterprise Business Owner prior to formal Phase 1 closure and Phase 2 architecture authorization.

---

## 2. Strategic Decisions Summary Register

| Decision ID | Linked BRD ID | Strategic Decision Area | Technical Team Proposal (NON-BINDING) | Business Owner Decision | Current Status |
| :---: | :---: | :--- | :--- | :---: | :---: |
| **`ADQ-001`** | [`OQ-001`](../08-assumptions-dependencies-open-questions.md#oq-001) | Operating Country & Geographic Scope | Option A: Domestic India Only at Launch | **Option A — Domestic India Only at Launch** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-002`** | [`OQ-002`](../08-assumptions-dependencies-open-questions.md#oq-002) | Transaction Currencies & Language Scope | Option A: Single INR (₹) & English Only | **Option A — Single INR (₹) & English Only** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-003`** | [`OQ-006`](../08-assumptions-dependencies-open-questions.md#oq-006) | Cash on Delivery (COD) Policy & Safeguards | Option B: Restricted COD (₹10,000 Cap + OTP) | **Option B — Restricted COD (₹10,000 cap, delivery OTP, ₹50 non-refundable fee)** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-004`** | [`OQ-008`](../08-assumptions-dependencies-open-questions.md#oq-008) | Electronics Return & Inspection Policy | Option B: 7-Day Window + Diagnostic Gating | **Option B — 7-day return/refund or replacement with technician diagnostic gate** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-005`** | [`OQ-009`](../08-assumptions-dependencies-open-questions.md#oq-009) | Tax Presentation & Catalog Pricing | Option A: Indian GST 100% Tax-Inclusive MRP | **Option A — GST-inclusive storefront pricing with tax breakdown** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-006`** | [`OQ-010`](../08-assumptions-dependencies-open-questions.md#oq-010) | Customer Registration & Auth Identifier | Option A: Mobile-First with SMS OTP | **Option A — Mobile-first with SMS OTP; email optional** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-007`** | [`OQ-016`](../08-assumptions-dependencies-open-questions.md#oq-016) | Peak Festive Surge Capacity Target | Option B: 100 OPM Sustained / 1,000 OPM Peak | **Option B — 100 orders/min sustained; 1,000 orders/min peak** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-008`** | [`OQ-017`](../08-assumptions-dependencies-open-questions.md#oq-017) | Platform Availability SLA & DR Targets | Option A: 99.9% Uptime, RPO $\le 5\text{m}$, RTO $\le 2\text{h}$ | **Option A — 99.9% uptime, RPO ≤ 5 minutes, RTO ≤ 2 hours** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-009`** | [`OQ-003`](../08-assumptions-dependencies-open-questions.md#oq-003) | Mandatory Customer Payment Methods | Option B: UPI, cards, net banking, and EMI/BNPL | **Option C — Option A payment methods plus COD under OQ-006 policy** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-010`** | [`OQ-004`](../08-assumptions-dependencies-open-questions.md#oq-004) | Payment Gateway Provider | Option B: Dual gateway | **Single gateway — Razorpay only (single-gateway approach / Option A)** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-011`** | [`OQ-005`](../08-assumptions-dependencies-open-questions.md#oq-005) | Shipping Management Approach (historical) | Earlier provider-mix restriction | **Historical decision; provider-mix restrictions superseded by ADQ-014. Chellaa-owned Shipping Management System remains in scope.** | **`SUPERSEDED_FOR_PROVIDER_MIX`** |
| **`ADQ-012`** | [`DEP-002`](../08-assumptions-dependencies-open-questions.md#dep-002) | India-Wide Delivery Target | India-wide intended delivery | **India-wide target; exact serviceability is phased and validated** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-013`** | [`DEP-002`](../08-assumptions-dependencies-open-questions.md#dep-002) | Delivery Rollout Method | Activate areas in phases | **Phased PIN-code activation after readiness validation** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-014`** | [`DEP-002`](../08-assumptions-dependencies-open-questions.md#dep-002) | Delivery Provider Model | Hybrid provider model | **Own team + direct couriers + shipping aggregator permitted; Chellaa owns canonical shipment lifecycle** | **`OWNER_DECISION_RECORDED`** |
| **`ADQ-015`** | [`OQ-007`](../08-assumptions-dependencies-open-questions.md#oq-007) | Initial Warehouse Topology | Single central vs multiple regional warehouses | **Four initial warehouse locations with regional inventory; exact sites and PIN codes pending validation; central design remains location-agnostic** | **`OWNER_DECISION_RECORDED; SITE_VALIDATION_OPEN`** |
---

## 3. Detailed Decision Sheets (ADQ-001 through ADQ-015; superseded history retained)

---

### ADQ-001: Operating Country & Geographic Scope

* **Decision ID:** `ADQ-001`
* **Linked BRD Open Question:** [`OQ-001: Operating Country & Domestic vs Cross-Border Scope`](../08-assumptions-dependencies-open-questions.md#oq-001)
* **Affected Business Domains:** Business Scope ([`01-business-scope.md`](../01-business-scope.md)), Tax & Invoicing, Logistics, Legal Compliance.
* **Affected Requirement IDs:** [`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001), [`FEAT-SHP-001`](../03-business-feature-inventory.md#feat-shp-001), [`FR-FIN-001`](../04-functional-requirements.md#fr-fin-001).
* **Business Question:** What geographic territories will the Chellaa retail platform serve at initial production launch?

#### Available Options and Consequences:
1. **Option A: Domestic India Only at Launch**
   * *Consequences:* Restricts platform operations to the Republic of India. Simplifies statutory compliance exclusively to Indian Goods and Services Tax (GST) rules, national PIN code registries, and domestic courier APIs. Eliminates foreign currency volatility, customs duty processing, and cross-border consumer protection complexities.
2. **Option B: India Primary with Selected Cross-Border Shipping (e.g., Middle East / South Asia)**
   * *Consequences:* Expands market reach, but mandates multi-jurisdictional tax engines, international customs documentation, international air waybills, export compliance ledgers, and heightened cross-border return logistics costs.
3. **Option C: Global International E-Commerce Launch**
   * *Consequences:* Requires multi-country tax compliance (VAT, US Sales Tax), multi-currency settlement banking, global carrier integrations, and worldwide consumer privacy regulations (GDPR, etc.), drastically increasing launch complexity and capital expenditure.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option A (Domestic India Only at Launch)`**
  * *Rationale:* Allows the enterprise to master single-warehouse physical operations, domestic courier integrations, and national statutory GST compliance before absorbing cross-border friction.

#### Business Owner Decision Record:
* [x] **Option A: Domestic India Only at Launch**
* [ ] **Option B: India Primary with Cross-Border Shipping**
* [ ] **Option C: Global International Retail Launch**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-002: Transaction Currencies & Language Scope

* **Decision ID:** `ADQ-002`
* **Linked BRD Open Question:** [`OQ-002: Currencies and Language Support`](../08-assumptions-dependencies-open-questions.md#oq-002)
* **Affected Business Domains:** Product Catalog, Pricing, Checkout, Notifications.
* **Affected Requirement IDs:** [`FEAT-CAT-001`](../03-business-feature-inventory.md#feat-cat-001), [`FEAT-PAY-001`](../03-business-feature-inventory.md#feat-pay-001), [`NFR-LOC-001`](../07-non-functional-requirements.md#nfr-loc-001).
* **Business Question:** What transaction currencies and languages must be supported across customer storefront and administrative interfaces?

#### Available Options and Consequences:
1. **Option A: Single Currency (INR - ₹) and Single Language (English) for Initial Baseline**
   * *Consequences:* Clean, single-currency accounting ledgers in the database. Eliminates real-time exchange rate API dependencies, currency conversion spread losses, and complex multi-lingual content catalogs.
2. **Option B: Single Currency (INR - ₹) with Multi-Language Storefront (English, Hindi, Tamil, Telugu)**
   * *Consequences:* Expands regional consumer accessibility across India, but requires multi-lingual catalog schema attributes, localized notification templates, and translation workflow tooling.
3. **Option C: Multi-Currency (INR, USD, AED, EUR) with Live Exchange Rate Conversion**
   * *Consequences:* Requires dual-currency ledgers (base currency vs transaction currency), payment gateway foreign exchange fee handling, and cart price expiration locking.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option A (Single Currency INR and English Only for Baseline)`**
  * *Rationale:* Preserves backend architectural simplicity and accounting integrity during launch; multi-lingual localization can be introduced cleanly in subsequent frontend iterations without database restructuring.

#### Business Owner Decision Record:
* [x] **Option A: Single Currency (INR) and English Only**
* [ ] **Option B: Single Currency (INR) with Regional Indian Languages**
* [ ] **Option C: Multi-Currency with Real-Time Conversion**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-003: Cash on Delivery (COD) Policy & Safeguards

* **Decision ID:** `ADQ-003`
* **Linked BRD Open Question:** [`OQ-006: Cash on Delivery (COD) Policy`](../08-assumptions-dependencies-open-questions.md#oq-006)
* **Affected Business Domains:** Checkout, Payments, Shipping, Order Management.
* **Affected Requirement IDs:** [`FEAT-CHK-001`](../03-business-feature-inventory.md#feat-chk-001), [`FEAT-PAY-001`](../03-business-feature-inventory.md#feat-pay-001), [`BR-ORD-003`](../05-business-rules.md#br-ord-003).
* **Business Question:** Will Cash on Delivery (COD) be offered to retail customers, and what commercial risk mitigation safeguards will be enforced?

#### Available Options and Consequences:
1. **Option A: 100% Prepaid Only at Launch (Zero COD)**
   * *Consequences:* Completely eliminates Return-to-Origin (RTO) cash delivery losses, courier cash handling fees (₹30–₹50/order), cash remittance reconciliation delays (7–14 days), and cash theft risks. However, may increase customer cart drop-off among first-time buyers who distrust prepaid orders.
2. **Option B: Restricted COD (Order Ceiling $\le ₹10,000$, Delivery OTP Verification, Non-Refundable ₹50 Convenience Fee)**
   * *Consequences:* Provides market-standard accessibility for mainstream electronics while shielding the enterprise from high-ticket fraud (e.g., ₹80,000 laptops). Requires courier cash collection reconciliation state machines and delivery OTP validation protocols.
3. **Option C: Unrestricted COD on All Orders**
   * *Consequences:* Maximizes initial order placement, but exposes the enterprise to catastrophic working capital lockups and high electronics RTO rates ($> 20\%$).

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option B (Restricted COD with ₹10,000 Ceiling and Delivery OTP)`**
  * *Rationale:* Establishes consumer trust in the brand while enforcing rigid financial firewalls against high-value logistics fraud.

#### Business Owner Decision Record:
* [ ] **Option A: 100% Prepaid Only (Zero COD)**
* [x] **Option B: Restricted COD (Max ₹10,000 + Delivery OTP + Non-Refundable Fee)**
* [ ] **Option C: Unrestricted COD**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-004: Electronics Return & Inspection Policy

* **Decision ID:** `ADQ-004`
* **Linked BRD Open Question:** [`OQ-008: Customer Returns and Inspection Policy`](../08-assumptions-dependencies-open-questions.md#oq-008)
* **Affected Business Domains:** Returns, Replacements & Refunds, Warehouse Operations, Financial Invoicing.
* **Affected Requirement IDs:** [`FEAT-RET-001`](../03-business-feature-inventory.md#feat-ret-001), [`FR-RET-001`](../04-functional-requirements.md#fr-ret-001), [`BR-RET-001`](../05-business-rules.md#br-ret-001), [`BR-RET-002`](../05-business-rules.md#br-ret-002).
* **Business Question:** What is the precise return and replacement policy for consumer electronics, and what physical verification gates customer refunds?

#### Available Options and Consequences:
1. **Option A: 7-Day Replacement Only for Verified Hardware Defects (Zero Remorse Returns)**
   * *Consequences:* Heavily protects enterprise profit margins and prevents "buyer's remorse" returns on opened electronics. Lowers warehouse grading overhead, but may generate consumer friction if a customer genuinely requires an exchange.
2. **Option B: 7-Day Return for Refund or Replacement with Mandatory Warehouse Technician Diagnostic Grading Gate**
   * *Consequences:* Customer may request a return within 7 calendar days of delivery. Reverse pickup is booked via courier. **Refund is strictly blocked until the parcel arrives at the warehouse, the serial/IMEI is scanned against the dispatched order record, and a technician certifies the hardware is intact.** Aligns with industry standards (Croma, Reliance Digital).
3. **Option C: 14-Day Open Return Policy with Instant Pickup Refund**
   * *Consequences:* High consumer satisfaction, but catastrophic risk in consumer electronics (fraudulent box swapping, stolen accessories, brick-in-box scams, damaged seals).

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option B (7-Day Window with Mandatory Technician Diagnostic Grading Gate)`**
  * *Rationale:* Balances consumer fairness with robust enterprise fraud protection; prevents unbacked payment refunds before verified physical inspection.

#### Business Owner Decision Record:
* [ ] **Option A: 7-Day Replacement Only (No Refunds)**
* [x] **Option B: 7-Day Return/Refund Gated on Technician Diagnostic Inspection**
* [ ] **Option C: 14-Day Open Return Policy with Instant Pickup Refund**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-005: Tax Presentation & Catalog Pricing Conventions

* **Decision ID:** `ADQ-005`
* **Linked BRD Open Question:** [`OQ-009: Tax Structure and Retail Pricing Conventions`](../08-assumptions-dependencies-open-questions.md#oq-009)
* **Affected Business Domains:** Merchandising, Pricing, Cart, Tax & Invoicing.
* **Affected Requirement IDs:** [`FEAT-PRC-001`](../03-business-feature-inventory.md#feat-prc-001), [`FEAT-FIN-001`](../03-business-feature-inventory.md#feat-fin-001), [`BR-FIN-001`](../05-business-rules.md#br-fin-001).
* **Business Question:** How must product prices and statutory taxes be displayed to retail customers and calculated across checkout and invoicing?

#### Available Options and Consequences:
1. **Option A: Indian GST Compliant; 100% Tax-Inclusive Storefront MRP Display**
   * *Consequences:* Every catalog, search, and cart price shown to retail consumers is fully inclusive of statutory GST (CGST/SGST or IGST). At checkout and on sequential tax invoices, the system executes mathematical reverse extraction to display the net taxable amount and GST breakdown. Meets Indian Consumer Protection (E-Commerce) Rules and Legal Metrology Act.
2. **Option B: Tax-Exclusive Display (Tax Added at Checkout)**
   * *Consequences:* Displays lower upfront prices, but introduces "bill shock" at checkout when 18% or 28% GST is added to the cart total. High risk of regulatory non-compliance in domestic Indian retail.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option A (100% Tax-Inclusive Storefront Pricing with Reverse Extraction)`**
  * *Rationale:* Enforces statutory compliance with Indian consumer protection laws and avoids checkout abandonment caused by surprise tax additions.

#### Business Owner Decision Record:
* [x] **Option A: 100% Tax-Inclusive Storefront Pricing (Reverse Extraction Invoicing)**
* [ ] **Option B: Tax-Exclusive Storefront Pricing (Tax Added at Checkout)**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-006: Customer Registration & Authentication Credential

* **Decision ID:** `ADQ-006`
* **Linked BRD Open Question:** [`OQ-010: Customer Account Verification Channels`](../08-assumptions-dependencies-open-questions.md#oq-010)
* **Affected Business Domains:** Customer Accounts, Security, Communications.
* **Affected Requirement IDs:** [`FEAT-CUST-001`](../03-business-feature-inventory.md#feat-cust-001), [`FR-CUST-001`](../04-functional-requirements.md#fr-cust-001), [`FR-SEC-001`](../04-functional-requirements.md#fr-sec-001).
* **Business Question:** What primary credential and verification channel will be required for retail customer registration and session authentication?

#### Available Options and Consequences:
1. **Option A: Mobile-First with SMS OTP Verification; Email Optional**
   * *Consequences:* Delivers lowest onboarding friction for Indian mobile-first consumers. Automatically captures a verified 10-digit mobile number essential for courier delivery updates and delivery OTP validation. Introduces ongoing telecom SMS gateway per-message costs.
2. **Option B: Email Address Mandatory with Password / Verification Link; Mobile Optional**
   * *Consequences:* Zero per-signup SMS gateway cost, but results in high customer drop-off during registration and risks delivery failures if customers provide inaccurate phone numbers at checkout.
3. **Option C: Both Mobile Number (SMS OTP) and Email Address Mandatory at Signup**
   * *Consequences:* Captures complete customer profiles, but introduces maximum registration friction and drop-off.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option A (Mobile-First with SMS OTP; Optional Email)`**
  * *Rationale:* Delivers highest customer conversion rates in the Indian market while guaranteeing verified phone numbers for courier dispatch handshakes.

#### Business Owner Decision Record:
* [x] **Option A: Mobile-First with SMS OTP (Email Optional)**
* [ ] **Option B: Email Mandatory with Password (Mobile Optional)**
* [ ] **Option C: Both Mobile (SMS OTP) and Email Mandatory**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-007: Peak Festive Surge Order Throughput

* **Decision ID:** `ADQ-007`
* **Linked BRD Open Question:** [`OQ-016: Expected Peak Traffic and Order Volumes`](../08-assumptions-dependencies-open-questions.md#oq-016)
* **Affected Business Domains:** Performance, Scalability, Cloud Infrastructure Sizing.
* **Affected Requirement IDs:** [`NFR-SCL-001`](../07-non-functional-requirements.md#nfr-scl-001), [`NFR-PERF-001`](../07-non-functional-requirements.md#nfr-perf-001).
* **Business Question:** What order throughput volumes (baseline sustained vs promotional festive surge) must the platform infrastructure be engineered and load-tested to support?

#### Available Options and Consequences:
1. **Option A: Modest Initial Sizing (20 Orders/Min Baseline; 200 Orders/Min Peak)**
   * *Consequences:* Minimizes cloud hosting infrastructure expenses during the initial launch, but risks platform crashes and connection pool exhaustion during flash sales or influencer marketing spikes.
2. **Option B: High-Volume Retail Sizing (100 Orders/Min Baseline; 1,000 Orders/Min Festive Peak Surge)**
   * *Consequences:* Provides robust headroom for major festive sale promotions (Diwali, New Year) without degrading checkout response times ($p95 \le 1,000\text{ms}$). Requires Kafka partitioning, Redis cluster sizing, and database connection pool tuning.
3. **Option C: Tier-1 Mega-Scale Sizing (500 Orders/Min Baseline; 5,000 Orders/Min Peak)**
   * *Consequences:* Exceeds launch requirements for a single-retailer platform; substantially inflates monthly cloud infrastructure bills before customer demand materializes.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option B (100 OPM Sustained Baseline / 1,000 OPM Peak Surge)`**
  * *Rationale:* Establishes a realistic, resilient commercial capacity target that supports aggressive marketing campaigns without incurring enterprise mega-scale cloud wastage.

#### Business Owner Decision Record:
* [ ] **Option A: 20 OPM Baseline / 200 OPM Peak Surge**
* [x] **Option B: 100 OPM Baseline / 1,000 OPM Peak Surge**
* [ ] **Option C: 500 OPM Baseline / 5,000 OPM Peak Surge**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-008: Platform Availability SLA & Disaster Recovery Targets

* **Decision ID:** `ADQ-008`
* **Linked BRD Open Question:** [`OQ-017: Production Availability and Recovery Targets (RTO / RPO)`](../08-assumptions-dependencies-open-questions.md#oq-017)
* **Affected Business Domains:** Business Continuity, High Availability, SRE Infrastructure.
* **Affected Requirement IDs:** [`NFR-AVL-001`](../07-non-functional-requirements.md#nfr-avl-001), [`NFR-BCM-001`](../07-non-functional-requirements.md#nfr-bcm-001).
* **Business Question:** What platform availability SLA (uptime) and disaster recovery targets (RPO / RTO) are commercially required by the enterprise?

#### Available Options and Consequences:
1. **Option A: 99.9% Uptime SLA; $\text{RPO} \le 5\text{ minutes}$, $\text{RTO} \le 2\text{ hours}$ (Single-Region Multi-AZ with Continuous WAL Archiving & Cold Standby)**
   * *Consequences:* Delivers high commercial availability ($\le 8.76\text{ hours}$ downtime/year across maintenance windows). Highly cost-effective cloud topology with automated continuous database backups and Point-In-Time Recovery (PITR).
2. **Option B: 99.99% Uptime SLA; $\text{RPO} = 0$, $\text{RTO} \le 15\text{ minutes}$ (Multi-Region Active-Active Cloud Architecture)**
   * *Consequences:* Ultra-high availability ($\le 52.6\text{ minutes}$ downtime/year), but requires multi-region data replication, distributed cross-region consensus, and more than doubles cloud infrastructure costs.

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option A (99.9% Uptime, RPO $\le 5$ min, RTO $\le 2$ hours via Single-Region Multi-AZ)`**
  * *Rationale:* Provides excellent enterprise-grade disaster recovery and availability for a single-retailer D2C business model with sensible infrastructure expenditure.

#### Business Owner Decision Record:
* [x] **Option A: 99.9% Uptime SLA, RPO $\le 5\text{ min}$, RTO $\le 2\text{ hours}$ (Single-Region Multi-AZ)**
* [ ] **Option B: 99.99% Uptime SLA, RPO $= 0$, RTO $\le 15\text{ min}$ (Multi-Region Active-Active)**
* **Business Owner Rationale & Conditions:** None supplied by the Business Owner.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit Business Owner selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**

---

### ADQ-009: Mandatory Customer Payment Methods

* **Decision ID:** `ADQ-009`
* **Linked BRD Open Question:** [`OQ-003: Required Customer Payment Methods`](../08-assumptions-dependencies-open-questions.md#oq-003)
* **Affected Business Domains:** Checkout, Payments, Financial Reconciliation.
* **Affected Requirement IDs:** `FEAT-PAY-001`, `FR-PAY-001`, `BR-PAY-001`.
* **Business Question:** Which customer payment methods are mandatory for the production launch?

#### Available Options and Consequences

1. **Option A:** UPI (Intent + QR), Credit Cards, Debit Cards, and Net Banking.
2. **Option B:** Option A plus Buy-Now-Pay-Later (BNPL) and Card EMI options.
3. **Option C:** Option A plus Cash on Delivery (COD).

* **Technical Team Proposal:** **`NON-BINDING PROPOSAL: Option B (UPI, Cards, Net Banking, and Bank EMI)`.**
* **Business Owner Decision Record:**
  * [ ] **Option A:** UPI, Cards, Net Banking.
  * [ ] **Option B:** Option A plus BNPL and Card EMI.
  * [x] **Option C:** Option A plus COD.
* **COD Policy Dependency:** COD is governed by `ADQ-003` / `OQ-006`: restricted COD up to ₹10,000, delivery OTP, and ₹50 non-refundable convenience fee.
* **Business Owner Rationale & Conditions:** None supplied.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. This is a business decision record, not Phase 1 baseline approval or technical architecture approval.

---


### ADQ-010: Payment Gateway Provider

* **Decision ID:** `ADQ-010`
* **Linked BRD Open Question:** [`OQ-004: Payment Gateway Partner Selection`](../08-assumptions-dependencies-open-questions.md#oq-004)
* **Affected Business Domains:** Checkout, Payments, Refunds, Settlement Reconciliation, External Integrations.
* **Business Owner Decision:** **Razorpay only at launch.** No secondary/failover payment gateway is selected for the initial release.
* **Option Mapping Note:** The BRD's Option A is the single-gateway approach. The owner's phrase “Option B but Razorpay only” has been interpreted by its explicit provider constraint (Razorpay only), so the recorded architecture is single-gateway rather than dual-gateway. This does not authorize a second provider.
* **Implementation Conditions:** Merchant onboarding and production credentials; hosted/checkout integration; webhook signature verification; idempotent payment processing; payment status reconciliation; source refunds; settlement reconciliation; no raw card PAN/CVV storage.
* **Business Owner Rationale & Conditions:** None supplied beyond “Razorpay only.”
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. Razorpay merchant agreement and technical validation remain open dependencies; this is not Phase 1 or architecture approval.

---

### ADQ-011: Shipping Management Approach (Historical — Provider Mix Superseded by ADQ-014)

* **Decision ID:** `ADQ-011`
* **Linked BRD Open Question:** [`OQ-005: Shipping Management Approach`](../08-assumptions-dependencies-open-questions.md#oq-005)
* **Affected Business Domains:** Shipping & Delivery, Returns, Reverse Logistics, COD Reconciliation.
* **Business Owner Decision:** **Option A — build Chellaa's own Shipping Management System.** The business may use its own delivery team. No shipping aggregator will be used; direct carrier integrations are optional and require a separate decision. No courier partner has been selected.
* **Implementation Conditions:** Define shipment lifecycle, internal tracking events, delivery assignment and attempts, proof of delivery, cancellations, reverse logistics, COD collection/reconciliation, and exception handling. Treat carrier-specific API, serviceability, and SLA work as out of scope until a separate carrier decision is recorded.
* **Business Owner Rationale & Conditions:** The owner explicitly requested to develop the shipping feature in-house and not use Shiprocket or any shipping aggregator.
* **Decision Owner:** EZHIL SELVAN P. — Founder (selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. Partner agreement, API credentials, commercial terms, and integration validation remain open dependencies; this is not Phase 1 or architecture approval.
* **Supersession note:** The earlier exclusion of a shipping aggregator and the earlier limitation of direct-carrier integrations are superseded for the delivery-provider mix by `ADQ-014`. Chellaa's own Shipping Management System remains in scope; provider selection and readiness remain open.

---

### ADQ-012: Initial Delivery Coverage — India-Wide In-House Operations

* **Decision ID:** `ADQ-012`
* **Linked BRD Dependency:** [`DEP-002: Shipping Operations Readiness`](../08-assumptions-dependencies-open-questions.md#dep-002)
* **Related Assumption to Reassess:** [`ASM-004`](../08-assumptions-dependencies-open-questions.md#asm-004) proposes one primary central fulfillment warehouse; India-wide hybrid delivery may require a different topology, but no multi-warehouse decision is made here.
* **Affected Business Domains:** Shipping & Delivery, Fulfillment, Warehouse Operations, Workforce Planning, Delivery Fees, Reverse Logistics, Customer Experience.
* **Business Owner Decision:** **India-wide intended delivery scope using Chellaa's own delivery team and in-house Shipping Management System.** This decision set the geographic target; the hybrid provider model (own team + direct courier partners + shipping aggregator) is defined later by `ADQ-014`.
* **Interpretation boundary:** This sets the intended geographic scope; it does not assert that every Indian PIN code is immediately serviceable or that staffing, fleet, warehouse capacity, SLAs, or operational readiness have been validated. Checkout must be limited to explicitly enabled PIN codes in the internal serviceability registry.
* **Implementation / readiness conditions:** Record the actual serviceable PIN codes and phased rollout; confirm warehouse topology; define operating hours, delivery estimates/SLAs, fee policy, staff and backup capacity, fleet model, proof-of-delivery/retention rules, reverse logistics, exception handling, and service continuity. Verify each enabled area through end-to-end operational testing before enabling it in checkout.
* **Business Owner Rationale & Conditions:** The owner selected India-wide delivery by Chellaa's own team. No specific PIN-code list, rollout schedule, headcount, fleet, warehouse topology, fee, or service-level target was supplied with this decision.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. DEP-002 remains `OPEN`; this selection does not approve the BRD baseline or architecture.

---

### ADQ-013: India-Wide Delivery Rollout Method — Phased PIN-Code Activation

* **Decision ID:** `ADQ-013`
* **Linked BRD Dependency:** [`DEP-002: Shipping Operations Readiness`](../08-assumptions-dependencies-open-questions.md#dep-002)
* **Related Decision:** `ADQ-012` — India-wide intended delivery scope using Chellaa's own delivery team.
* **Affected Business Domains:** Shipping & Delivery, Fulfillment, Warehouse Operations, Workforce Planning, Checkout Serviceability, Customer Support, Reverse Logistics.
* **Business Owner Decision:** **Option B — activate serviceable PIN codes in phases, according to operational readiness.** The India-wide coverage goal remains; not all Indian PIN codes are assumed to be serviceable at launch.
* **Operating rule:** A PIN code can be enabled for checkout only after its serviceability and local operating capacity are recorded and the applicable readiness gate is passed. COD eligibility is checked separately. Unsupported or not-yet-enabled PIN codes must not be accepted for delivery.
* **Minimum readiness gate:** Confirm the serviceable PIN-code list, warehouse/fulfillment path, trained delivery staffing and backup, vehicle/run capacity, hours and delivery promise, fee and COD policy, customer notifications and proof of delivery, failed-delivery and reverse-logistics procedures, customer support and incident escalation, plus end-to-end test evidence and Operations Lead review.
* **Expansion control:** Review service performance and operational risks before each expansion. Numeric targets, review windows, pilot locations and rollout-wave sequence remain to be agreed; this decision does not invent them.
* **Business Owner Rationale & Conditions:** The owner selected phased activation based on readiness. No initial pilot PIN codes, wave count, target metrics, headcount, or launch dates were supplied.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. DEP-002 remains `OPEN`; this decision does not approve the BRD baseline or architecture.

---

### ADQ-014: Hybrid Delivery Provider Model — Own Team, Direct Couriers and Aggregator

* **Decision ID:** `ADQ-014`
* **Linked BRD Dependency:** [`DEP-002: Shipping Operations Readiness`](../08-assumptions-dependencies-open-questions.md#dep-002)
* **Supersedes / clarifies:** The provider-exclusion clauses in `ADQ-011`; retains Chellaa ownership of its Shipping Management System and canonical internal shipment lifecycle. `ADQ-012` (India-wide target) and `ADQ-013` (phased activation) remain in effect, with this decision adding a multi-state pilot and a hybrid provider model.
* **Affected Business Domains:** Shipping & Delivery, Checkout Serviceability, Fulfillment, Customer Experience, Returns / Reverse Logistics, COD and Provider Settlement Reconciliation.
* **Business Owner Decision:** **Option C — allow all three delivery channels: (1) Chellaa's own delivery team, (2) directly integrated courier partners, and (3) a shipping aggregator.** The target is India-wide delivery, with the initial pilot in selected areas across multiple states. COD and online payment must continue to follow the existing approved payment policies.
* **Provider and rollout boundary:** This is permission to use all three channels, not selection of a named courier or aggregator, a signed contract, proof of PIN-code coverage, or authorization to enable an untested integration. Activate only validated PIN codes and provider lanes after operational readiness checks. Chellaa's internal Shipment ID remains canonical; provider AWB/booking references are additional identifiers.
* **Payment-policy boundary:** Online payments remain Razorpay-only for UPI, cards and net banking under the existing decision. COD remains restricted to eligible orders up to ₹10,000, verified/eligible PIN codes, delivery OTP, and the ₹50 non-refundable convenience fee. Delivery provider choice does not change checkout payment eligibility or silently relax these rules.
* **Implementation / readiness conditions:** Define provider-neutral shipment states/events; routing and fallback rules; direct courier and aggregator selection; contracts, rates, serviceability, SLAs, API credentials, booking/label/tracking/webhook behavior; idempotency and duplicate-event handling; pickup/failed-delivery/RTO/returns; COD collection, remittance, deductions and reconciliation; customer tracking and notifications; privacy/evidence retention; provider outage handling; and end-to-end tests for every enabled pilot lane.
* **Business Owner Rationale & Conditions:** The owner selected the hybrid delivery model and multi-state pilot. No specific pilot states/PIN codes, courier/aggregator brand, contract, provider ranking/routing policy, fees, SLAs, or rollout dates were supplied.
* **Decision Owner:** EZHIL SELVAN P. — Founder (explicit selection recorded 2026-10-24).
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED`**. `DEP-002` remains `OPEN`; this decision does not approve the BRD baseline or architecture.

---

### ADQ-015: Four Initial Warehouses with Regional Inventory (Location-Agnostic Design)

* **Decision ID:** `ADQ-015`
* **Linked BRD Open Question:** [`OQ-007: Warehouse Topology at Launch`](../08-assumptions-dependencies-open-questions.md#oq-007)
* **Related Dependency:** [`DEP-002: Shipping Operations Readiness`](../08-assumptions-dependencies-open-questions.md#dep-002)
* **Business Owner Planning Decision:** **Four initial warehouse locations with regional inventory.**
* **Central-design constraint:** The architecture and domain model must be generic and configuration-driven. Do not hard-code city names, exact warehouse addresses, PIN codes, or specific providers into services or routing logic. Candidate city examples discussed during planning are not validated launch sites.
* **Still unvalidated:** Actual addresses, legal entity/GST registrations and state tax implications, lease/ownership, staffing, storage capacity, warehouse operating hours, SKU stock distribution, transfer processes, barcode hardware, serviceable PIN codes, and delivery-lane capacity.
* **Affected Business Domains:** Inventory, Warehouse Operations, Fulfillment Routing, Shipping, Tax and Invoicing.
* **Implementation / readiness conditions:** Model Warehouse as configurable data; scope stock by warehouse and SKU/variant; record inventory movements and transfers; allocate order lines only to eligible locations; support routing based on stock, serviceability, capacity, cost and delivery promise; preserve an auditable allocation decision; validate cross-warehouse/split-shipment rules before enabling them.
* **Decision boundary:** This records the planned count and regional inventory model. It does not validate any particular site or mean all four warehouses are operational. DEP-002 remains open until the applicable operational evidence is validated.
* **Decision Owner:** Business Owner / Operations Lead.
* **Decision Date:** `2026-10-24`
* **Status:** **`OWNER_DECISION_RECORDED; SITE_VALIDATION_OPEN`**. Phase 1 baseline remains `PENDING_APPROVAL`; architecture authorization remains `NOT_GRANTED`.

---

## 4. Executive Decision Ratification Record

*(Record only details explicitly supplied by the authorized Enterprise Business Owner. Do not invent a signature or authorization hash; a date may be recorded when the owner explicitly supplies it.)*

| Field | Business Owner Record |
| :--- | :--- |
| **Enterprise Business Owner Name** | EZHIL SELVAN P. |
| **Corporate Title** | Founder |
| **Owner Decision Date** | `2026-10-24` |
| **Owner Selections Recorded** | `[x] ADQ-001` &nbsp; `[x] ADQ-002` &nbsp; `[x] ADQ-003` &nbsp; `[x] ADQ-004`<br>`[x] ADQ-005` &nbsp; `[x] ADQ-006` &nbsp; `[x] ADQ-007` &nbsp; `[x] ADQ-008`<br>`[x] ADQ-009` &nbsp; `[x] ADQ-010` &nbsp; `[x] ADQ-011` &nbsp; `[x] ADQ-012` &nbsp; `[x] ADQ-013` &nbsp; `[x] ADQ-014` &nbsp; `[x] ADQ-015` |
| **Executive Comments & Provisos** | None supplied. |
| **Formal Signature / Authorization Hash** | Not provided — no signature or authorization hash supplied. |
