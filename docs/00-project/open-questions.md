# Open Questions and Architecture Decisions Pending

This document records architectural, business, and integration questions that require stakeholder confirmation, along with evaluated options, trade-offs, and interim recommendations.

---

## 1. Open Architectural & Business Questions

### OQ-01: Multi-Warehouse Order Fulfillment Strategy
- **Context**: The business owner operates multiple physical warehouses. If an order contains SKU A (located in Warehouse 1) and SKU B (located in Warehouse 2), how should fulfillment proceed?
- **Option A (Split Fulfillment)**: Create two distinct shipments with separate courier tracking numbers under the same single order. Single customer payment, but two packages arrive at the customer doorstep.
- **Option B (Consolidation Fulfillment)**: Transfer SKU B to Warehouse 1 before dispatching a single consolidated package to the customer. Increases delivery time by 2-3 days, saves courier costs.
- **Option C (Prohibit Mixed Carts)**: Block checkout if items cannot be fulfilled from a single warehouse. Degrades customer conversion.
- **Recommendation**: **Option A (Split Fulfillment within Single Owner)**. The order remains a single customer transaction; the Fulfillment Service dispatches separate shipments from respective warehouses. (Note: This is strictly internal fulfillment routing, NOT multi-vendor marketplace splitting).
- **Status**: Recommended for Phase 7/9; pending merchant logistics confirmation.

### OQ-02: Customer Wallet & Store Credit Model
- **Context**: When a customer returns an item, should the platform support an instant "Refund to Store Wallet" in addition to "Refund to Original Payment Method"?
- **Option A**: Implement an internal double-entry ledger Wallet Service. Instant refunds, higher customer retention, but adds regulatory ledger compliance requirements (RBI prepaid payment instrument norms).
- **Option B**: Exclusively refund to the customer's original payment source via payment gateway API. Zero regulatory wallet burden, simpler accounting.
- **Recommendation**: Defer Wallet Service to Phase 15. Initial production release (Phase 10) executes refunds directly to original payment methods.
- **Status**: Deferred to Phase 15 per project roadmap.

### OQ-03: WhatsApp Business API Provider Selection
- **Context**: Multi-channel notifications require WhatsApp Business API.
- **Option A (Direct Meta Cloud API)**: Directly integrate Meta's Graph API. Lowest cost per conversation, but requires internal management of webhook certificate renewals and phone number registration.
- **Option B (Business Solution Provider - BSP like Gupshup/Twilio)**: Faster onboarding, simplified REST abstraction, automated template submission, slightly higher per-message fee.
- **Recommendation**: Build an abstract `WhatsAppChannelAdapter` interface with initial implementation targeting standard BSP REST API, with fallback to Meta Cloud API via configuration.
- **Status**: Documented in `docs/10-notification/whatsapp-channel.md`.

### OQ-04: Analytical Reporting Store Selection
- **Context**: Historical sales and inventory trend reporting over millions of records.
- **Option A**: Dedicated read-model tables in PostgreSQL populated via Kafka consumer (CQRS). Simple operations, zero new infrastructure.
- **Option B**: Deploy ClickHouse or DuckDB for columnar OLAP queries. Superior speed on complex aggregations, but adds a new distributed database cluster.
- **Recommendation**: Start with **Option A (PostgreSQL CQRS read-model)**. Migrate to ClickHouse only if analytical queries exceed 500ms p95 under verified production load (governed by ADR-010).
- **Status**: Approved for Phase 0 baseline.
