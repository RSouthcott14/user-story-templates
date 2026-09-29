# API Response Patterns

**API:** Choose Pharmacy NextGen
**Spec:** OAS 3.0 — `https://localhost:7074/swagger/v1/swagger.json`

---

## Overview

Response patterns standardise how data is returned across all endpoints, ensuring consistency for client developers and supporting FHIR interoperability where applicable.

---

## Lookup / Reference Data Responses

Lookup endpoints return a **response object wrapping an array of coded concepts**. The response uses a DTO class (e.g., `SearchAllergyTerminologyResponse`) with a property containing the results.

### Response Structure

**Response DTO:**
```csharp
public class SearchAllergyTerminologyResponse
{
    public IReadOnlyList<CodedElement> Substances { get; init; } = [];
}
```

**Each item uses the `CodedElement` type:**
```csharp
public record CodedElement(string? Code, string? Display, string? System)
{
}
```

**JSON Response:**
```json
{
  "substances": [
    {
      "code": "14806",
      "display": "Penicillin",
      "system": "http://snomed.info/sct"
    },
    {
      "code": "373270004",
      "display": "Propofol",
      "system": "http://snomed.info/sct"
    }
  ]
}
```

**Fields:**
- Property name (e.g., `substances`) — camelCase, plural, matches the DTO property name
- `code`: [string] — Unique code within the system (SNOMED code, etc.)
- `display`: [string] — Human-readable label for the concept
- `system`: [string] — FHIR coding system URI (e.g. `http://snomed.info/sct`)

**HTTP Status:** `200 OK` — even if the array is empty

### Query Parameters

Use consistent parameter naming from [API-PATH-CONVENTIONS.md#query-parameter-naming](API-PATH-CONVENTIONS.md#query-parameter-naming):
- `q`: [string, required] — Search query (used for terminology searches)
- `limit`: [integer, optional] — Maximum results (defaults vary, e.g. 10)

**Example requests:**
```
GET /api/v1/allergies/substance?q=penicillin&limit=20
GET /api/v1/allergies/manifestations?q=rash&limit=10
GET /api/v1/consultations/ccm/conditions?q=diab&limit=10
```

### Example Endpoints

```
GET /api/v1/allergies/substance
GET /api/v1/allergies/manifestations
GET /api/v1/consultations/ccm/conditions
GET /api/v1/cas/conditions
```

### ValueSet Documentation

When a lookup endpoint retrieves data from a FHIR ValueSet, the story MUST document which ValueSet:

**In the story's Endpoint Details section, add a "Terminology Reference":**
```markdown
### Terminology Reference
**ValueSet Name:** `[TBD - confirm with Terminology team]`  
**ValueSet ID:** `[TBD - confirm with Terminology team]`  
**FHIR URI:** `[TBD - confirm with Terminology team]`
```

**What to fill in:**
- **ValueSet Name:** Human-readable name (e.g., "IPS Conditions", "Allergy Manifestations")
- **ValueSet ID:** Machine identifier used in terminology server (e.g., `ips-conditions`, `allergy-substances`)
- **FHIR URI:** Full FHIR URI for the ValueSet (e.g., `https://fhir.nhs.wales/ValueSet/ips-conditions`)

Use `[TBD - confirm with Terminology team]` as a placeholder if the exact ValueSet is not yet confirmed. This makes it explicit that the terminology team must validate these details before implementation.

---

## Single Resource Responses

Single resource endpoints return the resource as a JSON object.

### Success Response (200 OK)

```json
{
  "patientId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "nonce": "abc123",
  "consentStatus": "explicit-agreed",
  "agreedDateTime": "2026-09-15T10:30:00Z"
}
```

**HTTP Status:** `200 OK`

### Resource Not Found (404)

If a requested resource does not exist, return `404 Not Found`. Include minimal error details (see [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md)).

```json
{
  "error": "PATIENT_NOT_FOUND",
  "message": "Patient with ID 3fa85f64-5717-4562-b3fc-2c963f66afa6 not found"
}
```

---

## Paginated List Responses

When pagination is supported, wrap results with metadata:

```json
{
  "items": [
    { "id": 1, "name": "Item 1" },
    { "id": 2, "name": "Item 2" }
  ],
  "total": 150,
  "skip": 0,
  "limit": 10
}
```

**Fields:**
- `items`: [array] — The paginated result set
- `total`: [integer] — Total count of all items (not just this page)
- `skip`: [integer] — Offset of results
- `limit`: [integer] — Maximum results returned per request

**HTTP Status:** `200 OK`

---

## Empty Results

**For lookup/reference endpoints:**
- Return `200 + []` (empty array)
- No special error handling needed

**Example:**
```
GET /api/v1/consultations/ccm/conditions?searchTerm=xyz
→ 200 OK
→ []
```

**For single-resource GET endpoints:**
- Return `404 Not Found` with error details
- See [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md)

---

## Submission Responses (POST/PUT)

After successfully creating or updating a resource, return:

**201 Created (POST):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "resourceType": "Consultation",
  "status": "recorded",
  "createdDateTime": "2026-09-29T12:15:00Z"
}
```

**200 OK (PUT):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "resourceType": "Consultation",
  "status": "updated",
  "updatedDateTime": "2026-09-29T12:15:00Z"
}
```

---

## Validation Failure Responses

When request validation fails, return `400 Bad Request`. See [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md) for exact format.

---

## Response Headers

All successful responses MUST include:

| Header | Value | Purpose |
|---|---|---|
| `Content-Type` | `application/json` | Data format |
| `Cache-Control` | `private, no-cache` | Privacy & freshness (default) |

**Example:**
```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: private, no-cache
Date: Mon, 29 Sep 2026 12:15:00 GMT

[...]
```

For reference data endpoints that are cacheable (lookup conditions, allergies), the backend team may override `Cache-Control` to allow caching:
```
Cache-Control: public, max-age=300
```

---

## Consistency Rules

1. **Always use `application/json` Content-Type** — no XML, CSV, or other formats
2. **Always return arrays as JSON arrays** — never wrap in a `data` or `results` key at the top level for lookup endpoints
3. **Always use ISO 8601 for dates/times** — e.g. `2026-09-29T12:15:00Z`
4. **Always include error details in error responses** — see [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md)
5. **Empty results return 200, not 404** — for query endpoints (see "Empty Results" above)

---

## Related Standards

- [API-PATH-CONVENTIONS.md](API-PATH-CONVENTIONS.md) — URL structure and naming
- [API-ERROR-HANDLING.md](API-ERROR-HANDLING.md) — Error response format
- [API-CODE-SYSTEMS.md](API-CODE-SYSTEMS.md) — Code system definitions

