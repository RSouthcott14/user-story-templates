# API Path Conventions

**API:** Choose Pharmacy NextGen
**Spec:** OAS 3.0 — `https://localhost:7074/swagger/v1/swagger.json`

---

## Naming Principles

### Use domain names, not technical names

Resource groups are always named after the **domain concept** they represent — never after a technical implementation detail such as the data source, pattern, or layer.

| Do | Don't |
|---|---|
| `/api/v1/cas/conditions` | `/api/v1/valuesets/cas-conditions` |
| `/api/v1/allergies/substance` | `/api/v1/fhir/allergies/substance` |
| `/api/v1/patients/{patientId}/consultations/ems` | `/api/v1/commands/create-ems-consultation` |

This applies to all endpoint types — lookups, submissions, updates. The caller should see what the resource *is*, not how it is stored or retrieved.

### Casing and pluralisation

All new resource group segments must be:

- **Lowercase** — URLs are case-sensitive; PascalCase risks case-mismatch errors
- **Plural** — resource groups represent collections
- **Kebab-case for multi-word segments**

| Do | Don't |
|---|---|
| `/api/v1/patients` | `/api/v1/Patient` |
| `/api/v1/organisations` | `/api/v1/Organisations` |
| `/api/v1/pharmacies` | `/api/v1/Pharmacies` |
| `/api/v1/cas/conditions` | `/api/v1/cas/Conditions` |

> **Note:** Existing resource groups (`Patient`, `Organisations`, `Addresses`, `Pharmacies`) pre-date this rule and are not consistent with it. Do not replicate their casing in new endpoints.

---

## Base Path

All endpoints are prefixed with:

```
/api/v1/
```

The `v1` segment is the API version. New stories must use this prefix.

---

## Resource Groups

Endpoints are organised by resource. The current resource groups are:

| Resource Group | Base Path |
|---|---|
| Address | `/api/v1/Addresses` |
| Allergies | `/api/v1/allergies` |
| Organisation | `/api/v1/Organisations` |
| Patient | `/api/v1/Patient` |
| Pharmacy | `/api/v1/Pharmacies` |

---

## Patient-Scoped Endpoints

Resources that belong to a specific patient are nested under the patient identifier:

```
/api/v1/Patient/{patientId}/[resource]
```

**Examples:**
```
GET  /api/v1/Patient/{patientId}/gp-record
GET  /api/v1/Patient/{patientId}/allergies
POST /api/v1/Patient/{patientId}/allergies
GET  /api/v1/Patient/{patientId}/consent
POST /api/v1/Patient/{patientId}/consent
POST /api/v1/Patient/{patientId}/consultations/ems
```

### Consultation Endpoints

Consultation submissions follow this pattern:

```
POST /api/v1/Patient/{patientId}/consultations/{serviceType}
```

Where `{serviceType}` is the lowercase service identifier (e.g. `ems`, `cas`, `sttt`).

---

## Patient Identifier

Two identifiers are used depending on the operation:

| Identifier | Used For |
|---|---|
| `{patientId}` | Fetching or updating a known patient record |
| `{nhsNumber}` | Lookups by NHS Number |

**Examples:**
```
GET /api/v1/Patient/{patientId}
GET /api/v1/Patient/{nhsNumber}
PUT /api/v1/Patient/{nhsNumber}
```

---

## Service-Scoped Endpoints

Resources that are specific to a particular service (and not shared across services) are nested under the parent service context. These are **not patient-scoped** but are service-scoped reference data or lookups.

```
/api/v1/{parentContext}/{service}/{resource}
```

**Examples:**
```
GET  /api/v1/consultations/ccm/conditions
GET  /api/v1/consultations/ems/conditions
POST /api/v1/consultations/cas/clinicians
```

> **Use when:** A resource is only ever used by a specific service and would never be shared with other services. Do not use if the resource might be referenced by multiple services — use the general reference data pattern instead.

---

## Reference / Lookup Endpoints

Reference data and lookup endpoints use a **domain-named resource group** — not a technical prefix like `valuesets`. The resource group names the domain, and the sub-resource names the specific data being retrieved. Use this pattern for shared reference data.

```
GET /api/v1/{domain}/{data}
```

**Examples:**
```
GET /api/v1/allergies/substance
GET /api/v1/allergies/manifestations
GET /api/v1/cas/conditions
```

> **Use when:** A resource is shared across multiple services or multiple features and is not service-specific. For service-specific lookups, use the Service-Scoped Endpoints pattern.

---

## Search Endpoints

Search operations append `/search` to the resource path:

```
GET  /api/v1/Addresses/search
GET  /api/v1/Organisations/search
POST /api/v1/Patient/search
```

Use `GET` with query parameters for simple lookups. Use `POST` with a request body when search criteria are complex or sensitive (e.g. patient demographic search).

---

## HTTP Methods

| Method | Use For |
|---|---|
| `GET` | Read a resource or search |
| `POST` | Create a resource or submit a complex search |
| `PUT` | Replace/update an existing resource |

---

## Query Parameter Naming

Query parameters follow consistent naming conventions across all endpoints:

| Purpose | Parameter Name | Type | Example |
|---------|---|---|---|
| Search/query text | `q` | string | `?q=diabetes` |
| Result count limit | `limit` | integer | `?limit=10` |
| Skip/offset for pagination | `skip` | integer | `?skip=20` |
| Sort order | `sortBy` | string | `?sortBy=display` |
| Filter by status | `status` | string | `?status=active` |
| Filter by date range | `from`, `to` | date-time | `?from=2026-01-01&to=2026-12-31` |

**Naming rules:**
- Use **camelCase** for all query parameters
- Use **`q` for search/query text** (not `searchTerm`, `search`, or `term`)
- Avoid abbreviations beyond `q` for search
- Be explicit about filter intent (`status=active` not `active=true`)

**Examples:**
```
GET /api/v1/allergies/substance?q=penicillin&limit=20
GET /api/v1/consultations/ccm/conditions?q=diab&limit=10
GET /api/v1/Addresses/search?q=CF10&limit=5
```

---

## Path Segment Style

- Sub-resources within a patient path use **lowercase kebab-case**: `gp-record`, `consultations`
- Service type identifiers use **lowercase**: `ems`, `cas`, `sttt`
- Route parameters use **camelCase**: `{patientId}`, `{nhsNumber}`, `{odsCode}`, `{pharmacyAccountNumber}`
- Query parameters use **camelCase**: `searchTerm`, `limit`, `skip`, `sortBy`

---

## Notes

- `patientId` refers to the Choose Pharmacy internal patient record identifier, not the NHS Number
- The `unmatched` patient path (`POST /api/v1/Patient/unmatched`) is for creating provisional ("Bronze") patient records via the CDR where no NHS Number match exists
