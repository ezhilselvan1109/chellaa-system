# Communication Patterns

Overview of synchronous and asynchronous integration patterns across microservices.

---

## 1. Decision Matrix: Synchronous REST vs. Asynchronous Kafka

| Integration Scenario | Chosen Pattern | Protocol | Rationale |
| :--- | :--- | :--- | :--- |
| **Catalog Browsing & Search** | Synchronous | REST / HTTP/2 | User requires immediate sub-50ms data to render the screen. |
| **Add to Cart & Reprice** | Synchronous | REST / HTTP/2 | Real-time calculation of totals required for immediate UI feedback. |
| **Payment Webhook Callback** | Synchronous Inbound | REST / HTTPS | Gateway expects immediate HTTP 200/204 acknowledgment. |
| **Search Index Synchronization** | Asynchronous | Kafka Event | Eventual consistency; catalog updates should not fail if OpenSearch is momentarily slow. |
| **Multi-Channel Notifications**| Asynchronous | Kafka Event | Dispatches should not block transactional order processing. |
| **Analytical Reporting Views** | Asynchronous | Kafka Event | CQRS analytical aggregation happens out-of-band without burdening transactional databases. |
| **Checkout Orchestration** | Hybrid | REST + Kafka | Initial checkout submission is synchronous; subsequent fulfillment tracking is asynchronous event-driven. |
