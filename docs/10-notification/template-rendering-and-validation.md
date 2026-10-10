# Template Rendering and Variable Validation

Safe template interpolation, schema validation, and missing variable fail-safes.

---

## 1. Variable Validation Pipeline

Before sending a message to a provider adapter:
1. **Schema Check**: The template declares mandatory placeholder variables:
   ```json
   "requiredVariables": ["customerName", "orderNumber", "trackingUrl"]
   ```
2. **Payload Verification**: The notification engine checks that all declared placeholders exist in the incoming Kafka event payload.
3. **Fail-Fast Policy**: If any required variable is null or missing, the dispatch fails immediately with status `FAILED_VARIABLE_MISSING` and routes to the DLQ. The notification is NEVER dispatched with ugly unfilled placeholders (e.g. `"Hi {{customerName}}"`).
4. **HTML Sanitization**: All variable inputs injected into email templates pass through HTML entity escaping to eliminate script injection.
