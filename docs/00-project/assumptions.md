# Assumptions

This document lists the baseline business, technical, legal, and operational assumptions underlying the e-commerce platform architecture.

---

## 1. Business & Commercial Assumptions

1. **Single Legal Entity**: All operations, warehouses, catalog items, and revenues flow through a single legal commercial entity.
2. **Multi-Warehouse Fulfillment**: The owner operates one or more physical warehouses. Stock is reserved and dispatched from the warehouse optimal for the shipping address (or central hub if unassigned), but all warehouses share a unified corporate inventory ledger.
3. **Currency & Localization**: Initial primary deployment targets the Indian domestic market (INR currency, GST compliance, pincode postal system, `en-IN` and `ta-IN` locales). The architecture is inherently multi-currency and multi-locale capable.
4. **Third-Party Logistics (3PL)**: Physical shipping is executed via third-party courier aggregators or carriers (e.g. Shiprocket, Delhivery, BlueDart) through REST webhook integrations.
5. **Payment Gateway Settlement**: Customer payments are collected via third-party gateways (Razorpay, Stripe) into the merchant's corporate merchant account. Payouts from gateways to the merchant bank account occur out-of-band via gateway schedules.

---

## 2. Technical & Infrastructure Assumptions

1. **Cloud / Container Runtime**: Services run as Linux containers on Docker (local development) and Kubernetes (production).
2. **Network Topology**: All internal microservice traffic traverses a private Virtual Private Cloud (VPC) network. Only the API Gateway is exposed to the public Internet.
3. **Database Availability**: PostgreSQL databases run with read replicas and automated failover in production environments.
4. **Message Broker Reliability**: Apache Kafka is deployed in a multi-broker cluster (min. 3 nodes) with replication factor 3 and `min.insync.replicas=2` ensuring zero message loss on broker restarts.
5. **Object Storage**: High-durability S3-compatible object storage (MinIO for local dev, AWS S3 in production) stores catalog media assets, invoice PDFs, and review uploads with pre-signed upload URLs.
6. **Clock Synchronization**: All servers run NTP daemon with UTC system clocks; all database timestamps and Kafka event timestamps are stored in UTC (`TIMESTAMPTZ` / ISO-8601 UTC).

---

## 3. Operational & Security Assumptions

1. **Token Lifetime**: Customer JWT access tokens expire in 15 minutes; refresh tokens expire in 30 days and undergo one-time rotation upon exchange.
2. **Payment Webhook Asynchrony**: Payment completion is never finalized solely by client browser redirect; finalization requires cryptographic verification of the gateway webhook or explicit server-side gateway status inquiry.
3. **WhatsApp Compliance**: WhatsApp Business API messages follow Meta's 24-hour customer service window rules; outbound transactional alerts outside the window use pre-registered and approved utility message templates.
