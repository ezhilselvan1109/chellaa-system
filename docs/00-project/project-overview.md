# Project Overview

## 1. Executive Summary

This project implements a large-scale, production-ready, enterprise e-commerce platform designed from first principles for a **single-owner business**. The platform delivers a fast, seamless shopping experience for retail consumers across mobile and desktop devices, paired with a comprehensive, role-governed administration portal for corporate operations, merchandising, warehouse fulfillment, customer service, and business intelligence.

## 2. Business Model & Core Philosophy

### Single-Owner Operating Model
- **Centralized Ownership**: The platform is operated exclusively by one merchant enterprise. The merchant owns all product inventory, defines all catalog hierarchies, controls promotional campaigns and pricing policies, fulfills orders from enterprise warehouses, collects customer payments directly, and manages returns and customer care.
- **Strict Marketplace Prohibition**: Under no circumstances will this system incorporate multi-vendor or marketplace mechanisms. There are no seller accounts, no third-party vendor onboarding portals, no commission calculations, no multi-vendor order splittings, and no escrow/vendor settlement engines.
- **Enterprise Warehousing**: The platform supports multiple physical warehouses and fulfillment nodes, but all facilities, inventory allocations, and warehouse employees belong directly to the single business entity.

## 3. High-Level Architecture Overview

The system is architected as an **event-driven microservices ecosystem** leveraging:
- **Java 21 & Spring Boot 3.3+**: Providing resilient, strongly-typed domain microservices.
- **PostgreSQL 16**: Serving as the isolated ACID transactional store of truth for each microservice.
- **Apache Kafka 3.7**: Powering distributed domain events, asynchronous sagas, read-model materialization, and notification triggers.
- **OpenSearch 2.13**: Powering lightning-fast catalog search, faceted filtering, suggestions, and typo-tolerant discovery.
- **Redis 7.2**: Providing distributed rate limiting, ephemeral cart caching, idempotency locks, and session caching.
- **React 18 & TypeScript**: Powering two distinct web applications:
  1. `customer-web`: The high-performance, customer-facing storefront.
  2. `admin-web`: The secure operations and merchandising portal.

## 4. Architectural Tenets

1. **Database-Per-Service**: Every microservice owns its private database. Direct cross-service table joins and cross-database queries are strictly forbidden.
2. **Transactional Outbox & Idempotency**: All domain events are committed to an outbox table within local ACID database transactions and dispatched reliably to Kafka. Consumers enforce deduplication using idempotency keys.
3. **Orchestrated Sagas**: Critical multi-service workflows (such as Order Checkout and Inventory Allocation) are managed by dedicated orchestrators with deterministic compensation actions.
4. **Documentation as Source of Truth**: All implementations are strictly governed by the approved architecture, contracts, and workflows documented in this repository.
