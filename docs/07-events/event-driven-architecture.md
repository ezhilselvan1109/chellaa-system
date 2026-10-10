# Event-Driven Architecture

High-level architecture of asynchronous event propagation, decoupled choreography, and cross-service integration over Apache Kafka.

---

## 1. Event-Driven Principles

1. **Domain Events as Immutable Facts**: Events represent business occurrences that have already happened (past tense: `OrderCreated`, `PaymentCompleted`). Consumers cannot reject or alter events; they can only react to them.
2. **Decoupled Producers & Consumers**: The producing service publishes to an abstract Kafka topic with zero compile-time or runtime knowledge of which downstream services consume the event.
3. **Event Granularity**: Events carry the full domain entity state delta and essential aggregate snapshot (State-Carrying Event Transfer), minimizing the need for consumers to call back synchronously to the producer for supplementary data.
