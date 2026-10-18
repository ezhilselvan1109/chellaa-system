# Open Decision Impact Analysis & Governance Report

| Document Attribute | Details |
| :--- | :--- |
| **Audit Document ID** | BRD-IMPACT-01 |
| **Title** | Strategic Open Decisions, Requirements Dependency Mapping & Architectural Impact Analysis |
| **Target Repository** | `ezhilselvan1109/chellaa-system` (Branch: `dev`) |
| **Audit Target** | `docs/00-requirements/` (12 Baseline Documents) |
| **Audit Date** | 2026-10-18 |
| **Auditor** | Senior Business Analyst, Requirements Quality Engineer & Documentation Auditor |

---

## 1. Executive Decision Governance Overview

This document analyzes the 22 strategic commercial decisions identified in [08-assumptions-dependencies-open-questions.md](../08-assumptions-dependencies-open-questions.md). It maps each unresolved decision to its affected business domains, functional requirements, business rules, and non-functional specifications.

The Chellaa platform requirements baseline currently maintains a provisional status (`PROPOSED` or `UNDECIDED`) for items that depend on these pending decisions. This ensures that the technical architecture phase does not prematurely hardcode unapproved business policies.

---

## 2. High-Impact Unresolved Decisions Matrix

| Decision ID | Decision Topic | Status | Recommended Parameter | Affected Requirement IDs | Affected Documents | Architectural & Commercial Impact of Delaying |
| :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| **[OQ-001](../08-assumptions-dependencies-open-questions.md#oq-001)** | Operating Country Scope | `UNDER_REVIEW` | Domestic India only at launch | `FR-FIN-001`<br>`FR-SHP-001`<br>`BR-FIN-001` | Doc 01, Doc 04, Doc 05, Doc 07 | Dictates tax calculation engine (GST vs foreign VAT), customs data, and international currency handling. |
| **[OQ-002](../08-assumptions-dependencies-open-questions.md#oq-002)** | Currencies & Languages | `UNDER_REVIEW` | INR (₹) and English only | `FR-PRC-001`<br>`NFR-LOC-001` | Doc 01, Doc 04, Doc 07 | Multi-currency requires exchange rate feeds and foreign currency reconciliation ledgers. |
| **[OQ-003](../08-assumptions-dependencies-open-questions.md#oq-003)** | Mandatory Payment Methods | `UNDER_REVIEW` | UPI, Cards, Net Banking, EMI | `FR-PAY-001`<br>`BR-PAY-001` | Doc 01, Doc 04, Doc 05 | Defines payment gateway webhook contracts, card tokenization, and bank redirect flows. |
| **[OQ-004](../08-assumptions-dependencies-open-questions.md#oq-004)** | Payment Gateway Selection | `OPEN` | Dual Gateway (Razorpay + Cashfree) | `FEAT-PAY-001`<br>`FR-INT-001` | Doc 03, Doc 04, Doc 08 | Dictates specific API SDKs, webhook cryptographic signatures, and refund API parameters. |
| **[OQ-005](../08-assumptions-dependencies-open-questions.md#oq-005)** | Courier Logistics Partner | `OPEN` | Multi-carrier aggregator (Shiprocket) | `FEAT-SHP-001`<br>`FR-SHP-001` | Doc 01, Doc 03, Doc 04, Doc 08 | Influences PIN-code serviceability API checks, waybill barcode generation, and reverse pickup. |
| **[OQ-006](../08-assumptions-dependencies-open-questions.md#oq-006)** | Cash on Delivery (COD) Policy | `UNDER_REVIEW` | Restricted COD ($\le ₹10,000$ ceiling) | `FR-CHK-001`<br>`BR-CART-002`<br>`BR-SHP-002` | Doc 01, Doc 04, Doc 05, Doc 06 | Unrestricted COD creates massive RTO logistics costs; restricted COD requires OTP delivery checks. |
| **[OQ-007](../08-assumptions-dependencies-open-questions.md#oq-007)** | Warehouse Launch Topology | `UNDER_REVIEW` | Single central fulfillment warehouse | `FR-INV-001`<br>`BR-INV-001`<br>`BR-INV-002` | Doc 01, Doc 04, Doc 05, Doc 06 | Multi-warehouse requires order routing algorithms, split-shipment logic, and multi-state GSTINs. |
| **[OQ-008](../08-assumptions-dependencies-open-questions.md#oq-008)** | Electronics Return Policy | `UNDER_REVIEW` | 7-day window; defect verification | `FR-RET-001`<br>`BR-RET-001`<br>`BR-RET-002` | Doc 01, Doc 04, Doc 05, Doc 06 | Controls reverse logistics booking, warehouse diagnostic grading, and customer support workflows. |
| **[OQ-009](../08-assumptions-dependencies-open-questions.md#oq-009)** | Tax Structure & Display Prices | `UNDER_REVIEW` | Indian GST; 100% Tax-Inclusive display | `FR-FIN-001`<br>`BR-FIN-001`<br>`BR-PRC-001` | Doc 01, Doc 04, Doc 05, Doc 07 | Determines whether taxes are embedded in catalog MRP or calculated dynamically at cart checkout. |
| **[OQ-010](../08-assumptions-dependencies-open-questions.md#oq-010)** | Customer Verification Method | `UNDER_REVIEW` | Mobile phone + SMS OTP primary | `FR-CUST-001`<br>`BR-CUST-001` | Doc 02, Doc 03, Doc 04, Doc 06 | Dictates user identity model, identity provider configuration, and telecom SMS gateway budget. |
| **[OQ-011](../08-assumptions-dependencies-open-questions.md#oq-011)** | Guest Checkout Support | `UNDER_REVIEW` | Streamlined guest with auto-account | `ACT-010`<br>`FR-CART-001`<br>`FR-CHK-001` | Doc 02, Doc 03, Doc 04, Doc 06 | Governs session cart merging, anonymous order tracking, and customer conversion funnels. |
| **[OQ-012](../08-assumptions-dependencies-open-questions.md#oq-012)** | Customer Reviews Scope | `UNDER_REVIEW` | Deferred to Phase 2 (Frontend) | `FEAT-REV-001`<br>`FR-REV-001`<br>`BR-REV-001` | Doc 03, Doc 04, Doc 05 | If included in Phase 1, requires building review moderation queues and verified buyer check. |
| **[OQ-015](../08-assumptions-dependencies-open-questions.md#oq-015)** | Notification Channels | `UNDER_REVIEW` | SMS (OTP), Email (Invoice), WhatsApp (Order) | `FEAT-NTF-001`<br>`FR-NTF-001`<br>`BR-NTF-001` | Doc 03, Doc 04, Doc 05, Doc 06 | Determines telecom DLT registration, WhatsApp Business API agreements, and channel fallback logic. |
| **[OQ-016](../08-assumptions-dependencies-open-questions.md#oq-016)** | Peak Order Throughput Target | `UNDER_REVIEW` | 100 OPM baseline; 1,000 OPM peak | `NFR-SCL-001`<br>`NFR-PERF-002` | Doc 04, Doc 07 | Sizing of database connection pools, Redis caching tier, Kafka partitions, and Kubernetes nodes. |
| **[OQ-017](../08-assumptions-dependencies-open-questions.md#oq-017)** | Availability & Recovery SLA | `UNDER_REVIEW` | 99.9% Uptime; RPO $\le 5$m; RTO $\le 2$h | `OBJ-010`<br>`NFR-AVL-001`<br>`NFR-BCM-001` | Doc 00, Doc 07 | Controls whether single-region multi-AZ deployment suffices or expensive multi-region is required. |
| **[OQ-018](../08-assumptions-dependencies-open-questions.md#oq-018)** | Required Business Reports | `UNDER_REVIEW` | Standard daily sales, velocity, GST | `FEAT-RPT-001`<br>`FR-RPT-001`<br>`BR-RPT-001` | Doc 03, Doc 04, Doc 05, Doc 06 | Dictates read-model query projections, aggregation frequency, and export formats. |
| **[OQ-019](../08-assumptions-dependencies-open-questions.md#oq-019)** | Legacy Data Migration Scope | `UNDER_REVIEW` | Greenfield launch with catalog import | `FEAT-DAT-001`<br>`FR-DAT-001` | Doc 03, Doc 04 | Full legacy migration requires writing custom ETL cleansing scripts for historical orders. |

---

## 3. Confirmed Strategic Decisions Summary

The following 3 decisions have been formally evaluated and confirmed as the baseline operating standard:

| Decision ID | Confirmed Strategic Parameter | Business Justification & Enforcement Mechanism |
| :---: | :--- | :--- |
| **[OQ-013](../08-assumptions-dependencies-open-questions.md#oq-013)** | **Loyalty Rewards & Store Credits are Out of Scope for Phase 1** | Eliminates complex loyalty ledger accounting, point liability tracking, and wallet refund compliance from initial launch scope. |
| **[OQ-014](../08-assumptions-dependencies-open-questions.md#oq-014)** | **Pre-orders & Backorders are Strictly Excluded from Phase 1** | Enforces non-negative available stock rule (`BR-INV-001`). Eliminates supplier fulfillment delays and unfulfillable customer promises. |
| **[OQ-020](../08-assumptions-dependencies-open-questions.md#oq-020)** | **8-Year Financial Record Retention with Marketing PII Anonymization** | Balances statutory tax compliance (Indian Companies Act & GST rules requiring 8-year invoice retention) with DPDPA 2023 customer privacy rights (`BR-DAT-001`). |
| **[OQ-021](../08-assumptions-dependencies-open-questions.md#oq-021)** | **Automated CI with Mandatory Manual Production Release Approval** | Guarantees automated test verification gates while preserving executive business control over production deployments (`NFR-REL-001`). |
| **[OQ-022](../08-assumptions-dependencies-open-questions.md#oq-022)** | **Automated Alerting for $> 2\%$ Error Rate and Payment Gateway Dropouts** | Ensures rapid incident response without incurring the excessive operational overhead of an external 24/7 third-party NOC (`NFR-OBS-002`). |

---

## 4. Executive Decision Prerequisite for Phase 2

Before the engineering team can begin **Phase 2: Microservice Architecture & Technical Specifications**, the Enterprise Business Owner must formally sign off on the 6 critical commercial parameters highlighted in [brd-audit-report.md](./brd-audit-report.md) Section 5:
1. Domestic India territory confirmation ([OQ-001](../08-assumptions-dependencies-open-questions.md#oq-001)).
2. Restricted COD policy with ₹10,000 threshold ([OQ-006](../08-assumptions-dependencies-open-questions.md#oq-006)).
3. 7-day electronics return window with mandatory inspection ([OQ-008](../08-assumptions-dependencies-open-questions.md#oq-008)).
4. Mobile OTP onboarding ([OQ-010](../08-assumptions-dependencies-open-questions.md#oq-010)).
5. 1,000 OPM peak throughput capacity ([OQ-016](../08-assumptions-dependencies-open-questions.md#oq-016)).
6. 99.9% Uptime, RTO $\le 2\text{ hours}$, and RPO $\le 5\text{ minutes}$ ([OQ-017](../08-assumptions-dependencies-open-questions.md#oq-017)).
