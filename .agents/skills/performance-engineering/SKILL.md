---
name: performance-engineering
description: >-
  Use this skill to identify throughput bottlenecks, optimize SQL execution plans, design Redis caching layers,
  tune JVM/connection pool parameters, and plan load and stress testing using k6 or Gatling.
---

# Performance Engineering Skill

## 1. Purpose
Provides methodologies to profile, optimize, benchmark, and scale microservices and data pipelines to sustain high throughput and low p99 latency during peak commercial traffic events (flash sales, seasonal campaigns).

## 2. When to Use It
- Analyzing high-latency database queries using PostgreSQL `EXPLAIN (ANALYZE, BUFFERS)`.
- Designing Redis cache invalidation strategies (Cache-Aside, Write-Through).
- Tuning HikariCP database connection pools, Tomcat thread pools, and JVM garbage collectors.
- Conducting load and stress tests (e.g. k6, Gatling) for catalog search, cart mutations, and checkout flows.
- Preventing N+1 query problems in Spring Data JPA.

## 3. Required Inputs
- Target SLAs: p95 latency < 50ms (Catalog read), p99 latency < 200ms (Checkout write), throughput targets (e.g., 5,000 RPS).
- Query execution plans and memory profile dumps.
- Caching architecture specifications (`docs/04-architecture/caching-strategy.md`).

## 4. Procedure
1. **Identify Performance Budgets**:
   - Establish p50, p95, p99 latency thresholds and max CPU/Memory utilization limits per service.
2. **Database Query Optimization**:
   - Inspect JPA-generated queries; eliminate N+1 queries via `JOIN FETCH` or `@EntityGraph`.
   - Run `EXPLAIN ANALYZE` on PostgreSQL queries; ensure index scans replace sequential table scans.
3. **Caching Strategy Design**:
   - Implement Cache-Aside with Redis for hot catalog data, product detail pages, and promotion configurations.
   - Enforce explicit Time-To-Live (TTL) with jitter to prevent cache stampedes.
4. **Connection Pool & Thread Tuning**:
   - Size HikariCP connection pools appropriately: `connections = ((core_count * 2) + effective_spindle_count)`.
   - Avoid oversized connection pools that cause database CPU context switching.
5. **Load Testing Execution**:
   - Author k6 or Gatling load scripts simulating real customer journeys (Browse -> Add to Cart -> Apply Coupon -> Checkout).
   - Measure throughput saturation points and degradation cliffs.

## 5. Rules & Constraints
- Never make unverified performance claims; provide empirical test results and benchmarks.
- Cache data must never serve stale financial prices or invalid stock levels during final checkout verification.

## 6. Expected Outputs
- Performance profiling reports and SQL index recommendations.
- Runnable k6/Gatling benchmark scripts.

## 7. Validation Checklist
- [ ] Are all database queries protected against N+1 fetch cascades?
- [ ] Do all Redis cache keys have an explicit TTL?
- [ ] Does checkout perform pessimistic/optimistic checks directly against PostgreSQL (bypassing stale cache)?

## 8. Common Failure Modes
- Setting infinite Redis TTL leading to silent memory exhaustion.
- Cache stampede when popular items expire simultaneously during flash sales.

## 9. Authoritative Links
- [Caching Strategy](file:///d:/learning/Microservice/backend/docs/04-architecture/caching-strategy.md)
- [Database Indexing](file:///d:/learning/Microservice/backend/docs/05-database/database-indexing.md)
- [Scalability Strategy](file:///d:/learning/Microservice/backend/docs/04-architecture/scalability-strategy.md)
