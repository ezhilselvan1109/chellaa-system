# Performance and Load Testing

Load testing with k6, stress testing, soak testing, and latency benchmarking.

---

## 1. Load Testing Scenarios (k6 Scripts)

1. **Flash Sale Scenario (Spike Test)**:
   - Ramp up from 100 to 5,000 virtual users (VUs) within 30 seconds competing for 50 limited flash sale items.
   - Measures p95 latency, error rates, and database connection pool saturation.
2. **Catalog Browsing (Sustained Load)**:
   - 2,000 VUs executing search, autocomplete, and product page views continuously for 1 hour.
   - Asserts p95 response time < 50ms and zero memory leaks in JVM heaps.
