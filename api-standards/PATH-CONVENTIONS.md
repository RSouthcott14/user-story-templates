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

## Reference / Lookup Endpoints

Reference data and lookup endpoints use a **domain-named resource group** — not a technical prefix like `valuesets`. The resource group names the domain, and the sub-resource names the specific data being retrieved.

```
GET /api/v1/{domain}/{data}
```

**Examples:**
```
GET /api/v1/allergies/substance
GET /api/v1/allergies/manifestations
GET /api/v1/cas/conditions
```

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

## Path Segment Style

- Sub-resources within a patient path use **lowercase kebab-case**: `gp-record`, `consultations`
- Service type identifiers use **lowercase**: `ems`, `cas`, `sttt`
- Route parameters use **camelCase**: `{patientId}`, `{nhsNumber}`, `{odsCode}`, `{pharmacyAccountNumber}`

---

## Notes

- `patientId` refers to the Choose Pharmacy internal patient record identifier, not the NHS Number
- The `unmatched` patient path (`POST /api/v1/Patient/unmatched`) is for creating provisional ("Bronze") patient records via the CDR where no NHS Number match exists
