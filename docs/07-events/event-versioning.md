# Event Versioning Strategy

Evolution rules for Kafka schemas to ensure forward and backward compatibility.

---

## 1. Schema Evolution Guidelines

1. **Forward Compatibility**:
   - Consumers written for schema v1 can process events published in schema v2.
   - Rules: Never delete a field; never change an existing field's semantic type; new fields must be optional or provide defaults.
2. **Backward Compatibility**:
   - Consumers written for schema v2 can process historical events published in schema v1.
3. **Breaking Changes**:
   - If a breaking structural change is unavoidable, publish to a brand-new Kafka topic with a bumped version suffix (e.g. `commerce.orders.order-created.v2`).
   - Producers publish to v2; old consumers are migrated; v1 topic is decommissioned after consumer cutover.
