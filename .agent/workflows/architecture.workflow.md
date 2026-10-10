# Architecture Workflow

## 1. Purpose & Objective
Evaluate engineering trade-offs, define system and microservice boundaries, establish data ownership, and formally document architectural decisions through Architecture Decision Records (ADRs).

## 2. Prerequisites & Inputs
- Approved functional and non-functional requirements.
- Core business invariants (single-owner model).
- Established constraints and scalability targets.

## 3. Workflow Sequence

```text
Requirements & Constraints
            |
            v
Domain & Bounded Contexts
            |
            v
Candidate Architectures & Boundaries
            |
            v
Trade-off & Complexity Analysis
            |
            v
Architecture Decision Record (ADR)
            |
            v
System & Microservice Design
            |
            v
Data Ownership & Isolation Strategy
            |
            v
Synchronous & Asynchronous Contracts
            |
            v
Architecture Review Gate
```

### Detailed Activity Steps:
1. **Analyze Requirements**: Parse throughput, latency, reliability, and consistency requirements.
2. **Review Domain Boundaries**: Confirm that domain entities and transaction boundaries align with business capabilities.
3. **Formulate Candidate Architectures**: Compare alternative topologies (e.g. separate service vs shared service).
4. **Trade-off Analysis**: Weigh operational complexity, network overhead, developer ergonomics, and failure modes.
5. **Draft ADR**: Record the decision in `docs/16-adr/` following standard structure: Context, Options, Decision, Consequences, Status.
6. **Detail System Design**: Update `docs/04-architecture/system-architecture.md` and microservice topology diagrams.
7. **Verify Data Isolation**: Ensure strict database-per-service isolation; eliminate direct cross-database dependencies.
8. **Specify Integration Contracts**: Detail synchronous REST and Kafka event topologies.
9. **Architecture Review Gate**: Confirm that changes do not violate core principles (e.g., zero marketplace bleeding).

## 4. Responsible Skills
- `architecture-design`
- `domain-design`
- `database-design`
- `security-engineering`
- `performance-engineering`

## 5. Completion Gate
- ADR drafted and committed with clear rationale.
- Microservice boundaries clearly drawn with single responsibility.
- Zero circular dependencies in the service graph.
- Resiliency strategies (timeouts, retries, fallbacks) documented for every inter-service call.
