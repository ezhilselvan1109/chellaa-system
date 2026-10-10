# Documentation Workflow

## 1. Purpose & Objective
Establish and maintain complete, consistent, and implementation-ready documentation across all domains, architecture boundaries, and service contracts before any code is generated.

## 2. Prerequisites & Inputs
- Project mission and business requirements.
- Existing architecture documents, ADRs, and API/Event specifications.
- Stakeholder questions and domain model notes.

## 3. Workflow Sequence

```text
+----------------------------+
| 1. Inspect Repository      |
+----------------------------+
              |
              v
+----------------------------+
| 2. Identify Requirements   |
+----------------------------+
              |
              v
+----------------------------+
| 3. Identify Assumptions &  |
|    Open Questions          |
+----------------------------+
              |
              v
+----------------------------+
| 4. Define Business Rules   |
+----------------------------+
              |
              v
+----------------------------+
| 5. Define Domain Model     |
+----------------------------+
              |
              v
+----------------------------+
| 6. Define Architecture     |
+----------------------------+
              |
              v
+----------------------------+
| 7. Define Database         |
|    Ownership               |
+----------------------------+
              |
              v
+----------------------------+
| 8. Define API & Event      |
|    Contracts               |
+----------------------------+
              |
              v
+----------------------------+
| 9. Define Security &       |
|    Failure Handling        |
+----------------------------+
              |
              v
+----------------------------+
| 10. Validate Consistency   |
+----------------------------+
              |
              v
+----------------------------+
| 11. Review Documentation   |
+----------------------------+
              |
              v
+----------------------------+
| 12. Update Traceability    |
+----------------------------+
```

### Detailed Activity Steps:
1. **Inspect Repository**: Check current filesystem state, existing conventions, and configurations without overwriting valid work.
2. **Identify Requirements**: Analyze functional and non-functional requirements, user stories, and acceptance criteria.
3. **Identify Assumptions and Open Questions**: Document unknowns in `docs/00-project/open-questions.md`; never make silent unverified assumptions.
4. **Define Business Rules**: Codify business invariants for the single-owner model across all commerce capabilities.
5. **Define Domain Model**: Establish bounded contexts, entities, value objects, and domain events.
6. **Define Architecture**: Map system topology, inter-service interactions, caching, and resiliency patterns.
7. **Define Database Ownership**: Assign exclusive database ownership per service, modeling tables, constraints, and indexes.
8. **Define API and Event Contracts**: Create explicit REST specs with HTTP status codes and Kafka schemas with partition keys.
9. **Define Security and Failure Handling**: Specify RBAC, JWT validation, network encryption, saga compensation, and DLQ handling.
10. **Validate Consistency**: Verify cross-document harmony (e.g. event topic names match across producers and consumers).
11. **Review Documentation**: Verify clarity, completeness, and absence of superficial placeholders.
12. **Update Traceability**: Update `docs/01-requirements/requirements-traceability.md` and `docs/15-implementation/feature-matrix.md`.

## 4. Responsible Skills
- `documentation-engineering`
- `requirements-analysis`
- `domain-design`
- `architecture-design`
- `api-design`
- `database-design`
- `kafka-event-design`

## 5. Completion Gate
- No placeholder strings (e.g., "TODO", "TBD") remain in required sections.
- Traceability links every requirement to a domain context, owning service, database schema, and contract.
- Consistency review confirms zero conflicts.
