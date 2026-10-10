# Template Versioning

Lifecycle management and immutable versioning for message templates.

---

## 1. Versioning Protocol

1. **Immutable Published Versions**: Once a template version is marked `PUBLISHED`, its content is locked and immutable.
2. **Drafting Revisions**: Modifying a template creates a new `DRAFT` revision with an incremented version number (`version = version + 1`).
3. **Rollback**: Administrators can switch the active published version back to an older version instantly via Admin Web with zero downtime.
4. **Historical Rendering Traceability**: Every dispatched notification logs the exact `templateId` and `version` used during rendering for audit compliance.
