# API Standards

Reference documentation for how Choose Pharmacy NextGen APIs are structured. Use these when writing API user stories to ensure paths, methods, and conventions are consistent with the existing codebase.

**Swagger (local):** `https://localhost:7074/swagger/index.html`

---

## Contents

| File | Covers |
|---|---|
| [API-PATH-CONVENTIONS.md](API-PATH-CONVENTIONS.md) | Base path, versioning, resource groups, patient-scoped paths, service-scoped paths, reference data paths, query parameter naming, HTTP methods, path segment style |
| [API-RESPONSE-PATTERNS.md](API-RESPONSE-PATTERNS.md) | Lookup/reference data response structure, single resource responses, paginated lists, empty results, submission responses, response headers, consistency rules |
| [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md) | Error response format, HTTP status codes, error code naming conventions, message guidelines, acceptance criteria for errors |
| [API-CODE-SYSTEMS.md](API-CODE-SYSTEMS.md) | When to create a code system, naming rules, URI pattern, mapping to domain enumerations |

---

## How to Use

When generating an API story, reference the relevant file(s) here to ensure:
- The endpoint path follows the established pattern
- The correct HTTP method is used
- Patient-scoped vs non-patient-scoped paths are correctly structured

See [`COPILOT-INSTRUCTIONS.md`](../COPILOT-INSTRUCTIONS.md) for prompt guidance.
