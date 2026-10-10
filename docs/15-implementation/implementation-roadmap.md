# Implementation Roadmap

Phase-by-phase implementation plan with explicit prerequisites and deliverables.

---

## 1. Roadmap Overview

| Phase | Title | Scope Summary | Prerequisites | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Phase 0** | **Documentation & Architecture** | Complete requirements, business rules, architecture, contracts, skills, workflows, ADRs, readiness review. | Master Prompt | **COMPLETED** |
| **Phase 1** | **Engineering Foundation** | Repository structure, Maven multi-module/Gradle, Docker Compose backing services, CI workflows, shared libraries. | Phase 0 | Pending Approval |
| **Phase 2** | **Identity & Customer** | `identity-service`, `customer-service`, JWT token generation, MFA/OTP, customer address book. | Phase 1 | Pending |
| **Phase 3** | **Product Catalog** | `catalog-service`, categories, brands, variants, SKUs, S3 media upload pre-signed URLs. | Phase 2 | Pending |
| **Phase 4** | **Catalog Search** | `search-service`, OpenSearch cluster sync via Kafka CDC, faceted filtering, autocomplete. | Phase 3 | Pending |
| **Phase 5** | **Pricing & Promotions**| `pricing-service`, `promotion-service`, discount stacking engine, coupon redemption caps, tax calculation. | Phase 3 | Pending |
| **Phase 6** | **Cart & Wishlist** | `cart-service`, guest cart Redis caching, cart merging, live price revalidation, saved for later. | Phase 5 | Pending |
| **Phase 7** | **Checkout & Inventory** | `inventory-service`, multi-warehouse atomic stock hold (15m TTL), reservation reaper daemon. | Phase 6 | Pending |
| **Phase 8** | **Orders & Payments** | `order-service` (Saga Orchestrator), `payment-service`, Razorpay/Stripe webhooks, PDF tax invoice generation. | Phase 7 | Pending |
| **Phase 9** | **Shipping & Logistics** | `shipping-service`, pincode serviceability, 3PL courier AWB assignment, tracking milestones. | Phase 8 | Pending |
| **Phase 10**| **Returns & Refunds** | `return-service`, `refund-service`, 7-day policy window, warehouse QC inspection, gateway refunds. | Phase 9 | Pending |
| **Phase 11**| **Notification Platform**| `notification-service`, multi-channel templates (Email, SMS, WhatsApp Business, Push, In-App). | Phase 8 | Pending |
| **Phase 12**| **Reviews & Customer UGC**| `review-service`, verified-purchase reviews, star ratings, moderation queue. | Phase 10 | Pending |
| **Phase 13**| **Admin Operations Web**| `apps/admin-web`, management screens for catalog, pricing, orders, warehouse inventory, audits. | Phase 11 | Pending |
| **Phase 14**| **Reporting & Analytics**| `reporting-service`, streaming CQRS read-models, midnight financial reconciliation. | Phase 13 | Pending |
| **Phase 15**| **Advanced Capabilities**| Closed-loop customer wallet, gift cards, personalized recommendation engine. | Phase 14 | Deferred |
| **Phase 16**| **Production Readiness**| End-to-end chaos testing, k6 load benchmarking (5,000 RPS), security pen-testing, Kubernetes deployment. | Phase 14 | Pending |
