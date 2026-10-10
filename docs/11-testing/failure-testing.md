# Failure and Chaos Testing

Simulating network partitions, database outages, payment timeouts, and saga compensations.

---

## 1. Chaos Engineering Scenarios (Chaos Mesh / Toxiproxy)

1. **Payment Gateway Latency Injection**:
   - Inject 10,000ms latency on outbound payment calls. Asserts that the Order Service timeout trips after 3,000ms, aborts the checkout, and releases inventory holds within 5 seconds.
2. **PostgreSQL Network Partition**:
   - Sever network connectivity to PostgreSQL primary for 30 seconds. Asserts that Actuator readiness probe turns `DOWN`, Kubernetes traffic stops routing to affected pods, and pods recover cleanly once connectivity restores.
3. **Kafka Broker Kill**:
   - Terminate one Kafka broker during high publication load. Asserts that remaining 2 in-sync brokers continue accepting outbox events with zero message loss (`min.insync.replicas=2`).
