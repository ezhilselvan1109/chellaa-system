# API Versioning Strategy

Guidelines for URI versioning, backward compatibility, and deprecation schedules.

---

## 1. Versioning Protocol

1. **URI Path Versioning**:
   - The major version number is embedded directly in the URI path: `/api/v1/orders`.
2. **Backward-Compatible Changes (No Version Bump)**:
   - Adding new optional request fields.
   - Adding new response fields.
   - Adding new endpoints.
   - Clients must be resilient to unknown fields (`DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES = false`).
3. **Breaking Changes (Require New Major Version)**:
   - Renaming or deleting an existing field.
   - Changing a field's data type.
   - Adding a required parameter to an existing request.
   - Modifying URL routes.
   - Must be published under a new path: `/api/v2/orders`.

---

## 2. Deprecation Policy

- Deprecated endpoints return a standard `Sunset` HTTP header (`Sunset: Wed, 11 Nov 2026 00:00:00 GMT`) and `Deprecation: @1762819200`.
- Deprecated versions are maintained for a minimum 6-month sunset window before decommissioning.
