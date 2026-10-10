# Consistency Model

Defines the transactional and consistency guarantees across local service boundaries and distributed workflows.

---

## 1. Local ACID vs. Distributed Eventual Consistency

1. **Local Consistency (Strong ACID)**:
   - Within a single microservice boundary, operations on aggregate roots execute within a single local database transaction guaranteeing Atomicity, Consistency, Isolation, and Durability (ACID).
   - Read Committed isolation level is used by default in PostgreSQL, augmented with pessimistic row locks (`FOR UPDATE`) for concurrent stock reservations.
2. **Distributed Consistency (Eventual BASE)**:
   - Across microservice boundaries, operations follow Basically Available, Soft state, Eventual consistency (BASE).
   - Distributed business workflows achieve consistency via asynchronous event propagation over Kafka and Orchestrated Sagas.

## 2. Inconsistency Tolerances & SLAs

| Domain Flow | Consistency Target | Recovery / Convergence Mechanism |
| :--- | :--- | :--- |
| **Stock Allocation** | Immediate Strong ACID | Local `inventory_db` lock prevents overselling. |
| **Order Placement** | Orchestrated Saga | Compensating transaction cancels order if payment fails. |
| **Catalog to Search Sync**| Eventual (<2.0 seconds) | Kafka CDC consumer updates OpenSearch asynchronously. |
| **Customer Review Aggregates**| Eventual (<5.0 seconds) | Background worker recalculates star ratings on `ReviewPublished`. |
| **Analytical Reporting** | Eventual (<10.0 seconds)| Streaming CQRS consumers ingest events into `reporting_db`. |
