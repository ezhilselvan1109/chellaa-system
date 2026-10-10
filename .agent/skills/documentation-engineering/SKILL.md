---
name: documentation-engineering
description: >-
  Use this skill to maintain synchronized, high-quality, and implementation-ready project documentation,
  validate cross-document consistency, prevent placeholder text, and maintain requirements traceability.
---

# Documentation Engineering Skill

## 1. Purpose
Ensures that all architectural, domain, business rule, database, API, and event documentation remains accurate, comprehensive, mutually consistent, and actively serves as the single source of truth for all engineering implementations.

## 2. When to Use It
- Updating documentation alongside code or architectural changes.
- Reviewing documentation sets for gaps, ambiguities, or contradictions.
- Maintaining the requirements traceability matrix (`docs/01-requirements/requirements-traceability.md`).
- Updating the feature matrix (`docs/15-implementation/feature-matrix.md`) and implementation roadmap.
- Auditing documents to eradicate superficial placeholder content ("TODO", "TBD").

## 3. Required Inputs
- Architecture decisions, ADRs, and schema/contract updates.
- Requirements and business rule documents.
- Operating rules specified in `AGENTS.md`.

## 4. Procedure
1. **Audit Document Scope & Hierarchy**: Ensure documents are placed in the correct numbered category (`docs/00` to `docs/16`).
2. **Eliminate Placeholders**: Replace any placeholder text with concrete implementation schemas, contracts, or rule definitions. If a decision is genuinely open, link it explicitly to `docs/00-project/open-questions.md`.
3. **Verify Cross-Document Consistency**:
   - Check that service names, entity names, and database types match across all documents.
   - Verify that Kafka topic names and event schema fields match between producers and consumers.
   - Verify that REST endpoint routes in OpenAPI match routing rules in API Gateway specs.
4. **Maintain Traceability**:
   - Update `docs/01-requirements/requirements-traceability.md` ensuring every user story links to a service, schema, contract, and test.
5. **Update Progress & Matrix**:
   - Update `docs/15-implementation/feature-matrix.md` with accurate verified statuses.

## 5. Rules & Constraints
- Documentation is executable truth: never write code that deliberately deviates from documented architecture without updating the docs.
- Maintain Markdown standards: GitHub Flavored Markdown, valid table formatting, and valid relative links.

## 6. Expected Outputs
- Polished, fully detailed Markdown files in `docs/`.
- Updated traceability and feature matrix tables.

## 7. Validation Checklist
- [ ] Are all relative Markdown links functional and accurate?
- [ ] Are all business rules aligned with the strict single-owner e-commerce model?
- [ ] Are all API contracts accompanied by concrete JSON examples?

## 8. Common Failure Modes
- Documentation rotting while code moves forward in isolation.
- Silently changing a business rule without updating the corresponding business rules document.

## 9. Authoritative Links
- [Project Overview](file:///d:/learning/Microservice/backend/docs/00-project/project-overview.md)
- [Requirements Traceability](file:///d:/learning/Microservice/backend/docs/01-requirements/requirements-traceability.md)
- [Feature Matrix](file:///d:/learning/Microservice/backend/docs/15-implementation/feature-matrix.md)
