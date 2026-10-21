# API Story: POST/GET/PUT /api/v1/system-messages - Create, Retrieve, and Manage System Messages

## Story Type: API | Feature: 617546 | Effort: 21 hours

---

## Description

### Story Card
**As a** System Administration Backend  
**I want** to create, retrieve, update, and delete system messages with role-based visibility  
**So that** administrators can communicate important announcements, maintenance notifications, and role-specific alerts to platform users

### Background
System administrators require a secure API to manage system messages displayed to users. The API must support CRUD operations with comprehensive validation, role-based visibility control, message scheduling, and audit trail recording. This endpoint provides the backend for the System Message Management feature (617546), supporting the UI story for message creation and editing (617590).

### Objective
Deliver secure, validated CRUD endpoints for system message management with role-based visibility, message scheduling, audit recording, and standardized error handling.

---

## Endpoint Details

### List System Messages

**HTTP Method:** GET  
**Endpoint:** `/api/v1/system-messages`  
**Full URL:** `https://api.example.com/api/v1/system-messages`

**Query Parameters:**
- `skip`: [integer, optional, default: 0] - Number of messages to skip for pagination
- `limit`: [integer, optional, default: 10, max: 100] - Maximum messages per page
- `status`: [string, optional] - Filter by message status (draft, scheduled, active, expired, deleted)
- `priority`: [string, optional] - Filter by priority (standard, important)
- `sortBy`: [string, optional, default: createdDate] - Sort field (createdDate, priority, status)

### Get Single System Message

**HTTP Method:** GET  
**Endpoint:** `/api/v1/system-messages/{messageId}`  
**Full URL:** `https://api.example.com/api/v1/system-messages/{messageId}`

**Path Parameters:**
- `{messageId}`: [string, UUID, required] - System message ID

### Create System Message

**HTTP Method:** POST  
**Endpoint:** `/api/v1/system-messages`  
**Full URL:** `https://api.example.com/api/v1/system-messages`

### Update System Message

**HTTP Method:** PUT  
**Endpoint:** `/api/v1/system-messages/{messageId}`  
**Full URL:** `https://api.example.com/api/v1/system-messages/{messageId}`

**Path Parameters:**
- `{messageId}`: [string, UUID, required] - System message ID

### Delete System Message

**HTTP Method:** DELETE  
**Endpoint:** `/api/v1/system-messages/{messageId}`  
**Full URL:** `https://api.example.com/api/v1/system-messages/{messageId}`

**Path Parameters:**
- `{messageId}`: [string, UUID, required] - System message ID

---

## Request & Response Specifications

### Authorize-only Operations (Authentication & Authorization)

All endpoints require:
- **Authorization Header:** Bearer token with System Administrator role
- **Role Check:** Only users with System Administrator role can access any message management endpoint

### Create Message Request (POST)

```json
{
  "title": "COVID-19 Testing Service",
  "summary": "New COVID testing location has opened",
  "messageBody": "<p><strong>Important Update</strong> - Please review the new testing location details.</p>",
  "priority": "important",
  "startDateTime": "2026-06-10T09:00:00Z",
  "expiryDateTime": "2026-06-15T17:00:00Z",
  "visibleRoles": ["cas-pharmacist-admin", "cas-pharmacist"]
}
```

**Request Fields:**
- `title`: [string, required, max 200 chars] - Short message heading
- `summary`: [string, required, max 500 chars] - Brief overview displayed in listings
- `messageBody`: [string, required] - Main message content (HTML formatted)
- `priority`: [string, required, enum] - Standard or Important (see Code Systems)
- `startDateTime`: [ISO 8601 datetime, required] - When message becomes visible
- `expiryDateTime`: [ISO 8601 datetime, required] - When message expires
- `visibleRoles`: [array of strings, required, min 1 item] - Application roles permitted to view

### Create Message Response (201 Created)

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "COVID-19 Testing Service",
  "summary": "New COVID testing location has opened",
  "messageBody": "<p><strong>Important Update</strong> - Please review the new testing location details.</p>",
  "priority": "important",
  "startDateTime": "2026-06-10T09:00:00Z",
  "expiryDateTime": "2026-06-15T17:00:00Z",
  "visibleRoles": ["cas-pharmacist-admin", "cas-pharmacist"],
  "status": "scheduled",
  "createdBy": "admin-user-id",
  "createdDateTime": "2026-06-10T08:30:00Z",
  "lastUpdatedBy": null,
  "lastUpdatedDateTime": null,
  "deletedBy": null,
  "deletedDateTime": null
}
```

**Response Fields:**
- `id`: [string, UUID] — Unique message identifier
- `title`: [string] — Message heading
- `summary`: [string] — Message overview
- `messageBody`: [string] — Message content (HTML)
- `priority`: [string, enum] — Standard or Important
- `startDateTime`: [ISO 8601 datetime] — Visibility start time
- `expiryDateTime`: [ISO 8601 datetime] — Visibility end time
- `visibleRoles`: [array of strings] — Visible to these roles
- `status`: [string, enum] — Current message status (draft, scheduled, active, expired, deleted)
- `createdBy`: [string, UUID] — User ID who created the message
- `createdDateTime`: [ISO 8601 datetime] — Timestamp message was created
- `lastUpdatedBy`: [string, UUID, nullable] — User ID of last update
- `lastUpdatedDateTime`: [ISO 8601 datetime, nullable] — Last update timestamp
- `deletedBy`: [string, UUID, nullable] — User ID who deleted (if deleted)
- `deletedDateTime`: [ISO 8601 datetime, nullable] — Deletion timestamp

### Get Single Message Response (200 OK)

Same as Create Message Response above.

### List Messages Response (200 OK)

```json
{
  "items": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "title": "COVID-19 Testing Service",
      "summary": "New COVID testing location has opened",
      "priority": "important",
      "startDateTime": "2026-06-10T09:00:00Z",
      "expiryDateTime": "2026-06-15T17:00:00Z",
      "visibleRoles": ["cas-pharmacist-admin", "cas-pharmacist"],
      "status": "scheduled",
      "createdBy": "admin-user-id",
      "createdDateTime": "2026-06-10T08:30:00Z",
      "lastUpdatedBy": null,
      "lastUpdatedDateTime": null,
      "deletedBy": null,
      "deletedDateTime": null
    },
    {
      "id": "660f9410-f39c-52e5-c827-557766551111",
      "title": "System Maintenance Notice",
      "summary": "Scheduled maintenance on July 15th",
      "priority": "standard",
      "startDateTime": "2026-07-15T10:00:00Z",
      "expiryDateTime": "2026-07-15T16:00:00Z",
      "visibleRoles": ["cas-pharmacist-admin", "cas-pharmacist"],
      "status": "active",
      "createdBy": "admin-user-id",
      "createdDateTime": "2026-07-14T09:00:00Z",
      "lastUpdatedBy": null,
      "lastUpdatedDateTime": null,
      "deletedBy": null,
      "deletedDateTime": null
    }
  ],
  "total": 45,
  "skip": 0,
  "limit": 10
}
```

**Response Fields:**
- `items`: [array] — List of message objects (same schema as Get Single Message)
- `total`: [integer] — Total count of all messages
- `skip`: [integer] — Number of messages skipped
- `limit`: [integer] — Maximum messages returned per page

### Update Message Request (PUT)

```json
{
  "title": "COVID-19 Testing Service - Updated",
  "summary": "New COVID testing location has opened - Check hours",
  "messageBody": "<p><strong>Important Update</strong> - New testing location is now open 09:00-17:00.</p>",
  "priority": "important",
  "startDateTime": "2026-06-10T09:00:00Z",
  "expiryDateTime": "2026-06-30T17:00:00Z",
  "visibleRoles": ["cas-pharmacist-admin", "cas-pharmacist", "cas-superintendent-pharmacist"]
}
```

Same fields as Create Message Request.

### Update Message Response (200 OK)

Same schema as Get Single Message Response, with updated field values and `lastUpdatedDateTime`/`lastUpdatedBy` populated.

### Delete Message Request (DELETE)

No request body required.

### Delete Message Response (204 No Content)

Empty response body. Message is marked as deleted and removed from user visibility.

---

## Code Systems

### MessagePriority

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/message-priority`

| Code | Display |
|------|---------|
| `standard` | Standard - Routine informational communications |
| `important` | Important - High-profile communications (service outages, mandatory changes, critical notices) |

### MessageStatus

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/message-status`

**Note:** Status is calculated automatically by the system based on current date/time and scheduling dates. It is not set by the client.

| Code | Display |
|------|---------|
| `draft` | Draft - Saved but not yet published |
| `scheduled` | Scheduled - Awaiting start date |
| `active` | Active - Visible to users |
| `expired` | Expired - End date reached |
| `deleted` | Deleted - Removed by administrator |

### ApplicationRole

**System URI:** `https://fhir.nhs.wales/ChoosePharmacy/CodeSystem/application-role`

| Code | Display |
|------|---------|
| `cas-pharmacist` | CAS Pharmacist |
| `cas-pharmacist-admin` | CAS Pharmacist Administrator |
| `cas-superintendent-pharmacist` | CAS Superintendent Pharmacist |
| `community-pharmacist` | Community Pharmacist |
| `pharmacy-technician` | Pharmacy Technician |
| `health-board-user` | Health Board User |
| `national-administrator` | National Administrator |
| `system-administrator` | System Administrator |

---

## Validation Rules & Business Logic

### Mandatory Fields
- `title` is mandatory (must not be empty or null)
- `summary` is mandatory (must not be empty or null)
- `messageBody` is mandatory (must not be empty or null)
- `priority` is mandatory (must be one of: standard, important)
- `startDateTime` is mandatory (must be valid ISO 8601 datetime)
- `expiryDateTime` is mandatory (must be valid ISO 8601 datetime)
- `visibleRoles` is mandatory (must contain at least one role)

### Title Validation
- Must not exceed 200 characters
- Must not contain leading/trailing whitespace (trimmed)
- Must not contain malicious HTML/script content

### Summary Validation
- Must not exceed 500 characters
- Must not contain malicious HTML/script content

### Message Body Validation
- Allowed HTML tags only: `<p>`, `<strong>`, `<em>`, `<u>`, `<ul>`, `<ol>`, `<li>`, `<br>`, `<a>`, `<hr>`
- Must not contain script tags, event handlers, or other dangerous HTML
- Links (href) must be valid HTTP/HTTPS URLs

### Date/Time Validation
- `startDateTime` must not be later than `expiryDateTime`
- Both must be valid ISO 8601 format
- Dates must not be in the past when creating new messages
- When editing, dates can be adjusted as needed

### Role Validation
- `visibleRoles` must contain at least one valid role from the ApplicationRole code system
- All roles must be valid (not arbitrary strings)
- Duplicate roles in array are silently deduplicated

### Status Calculation
Status is automatically calculated based on current date/time:
- If `startDateTime` > now: Status = `scheduled`
- If `startDateTime` <= now < `expiryDateTime`: Status = `active`
- If now >= `expiryDateTime`: Status = `expired`
- If deleted: Status = `deleted`

---

## Error Responses

All errors follow ASP.NET Core `ProblemDetails` format as per [standards/API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md).

### Validation Error Response (400 Bad Request)

```json
{
  "type": "ValidationException",
  "title": "ValidationException",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "title": ["Title is required"],
    "expiryDateTime": ["Expiry date must be after the start date"]
  }
}
```

### Authorization Error Response (401 Unauthorized)

```json
{
  "type": "AuthenticationException",
  "title": "AuthenticationException",
  "status": 401,
  "detail": "Invalid or expired authorization token"
}
```

### Permission Denied Response (403 Forbidden)

```json
{
  "type": "UnauthorizedAccessException",
  "title": "UnauthorizedAccessException",
  "status": 403,
  "detail": "User does not have permission to manage system messages. Only System Administrators can access this endpoint."
}
```

### Message Not Found Response (404 Not Found)

```json
{
  "type": "EntityNotFoundException",
  "title": "EntityNotFoundException",
  "status": 404,
  "detail": "System message with ID 550e8400-e29b-41d4-a716-446655440000 not found"
}
```

### Message Already Exists Response (409 Conflict)

```json
{
  "type": "EntityAlreadyExistsException",
  "title": "EntityAlreadyExistsException",
  "status": 409,
  "detail": "A system message with this title already exists"
}
```

### Server Error Response (500 Internal Server Error)

```json
{
  "type": "Exception",
  "title": "Exception",
  "status": 500,
  "detail": "An unexpected error occurred while processing your request"
}
```

---

## Acceptance Criteria

**Total Scenarios: 16**

### Scenario 1: Create message with valid data

```gherkin
Given a valid bearer token for a System Administrator user
And the request contains all mandatory fields:
- title: "COVID-19 Testing Service"
- summary: "New COVID testing location has opened"
- messageBody: "<p>Testing location details</p>"
- priority: "important"
- startDateTime: "2026-06-10T09:00:00Z"
- expiryDateTime: "2026-06-15T17:00:00Z"
- visibleRoles: ["cas-pharmacist-admin", "cas-pharmacist"]
When POST /api/v1/system-messages is called
Then the API returns status 201
And the response includes the created message with all fields populated
And the id field is a valid UUID
And the status field is "scheduled" (since startDateTime is in the future)
And the createdBy field contains the authenticated user ID
And the createdDateTime is set to the current UTC time
And the lastUpdatedBy and lastUpdatedDateTime are null
And the deletedBy and deletedDateTime are null
And the message is persisted in the database
```

### Scenario 2: Create message with past start date

```gherkin
Given a valid bearer token for a System Administrator user
And the request contains a startDateTime in the past
When POST /api/v1/system-messages is called
Then the API returns status 201
And the response includes the created message
And the status field is "active" (since startDateTime is in the past and endDateTime is in the future)
And the message is persisted in the database
```

### Scenario 3: Create message with past expiry date

```gherkin
Given a valid bearer token for a System Administrator user
And the request contains an expiryDateTime in the past
When POST /api/v1/system-messages is called
Then the API returns status 201
And the response includes the created message
And the status field is "expired" (since expiryDateTime is in the past)
And the message is persisted in the database
```

### Scenario 4: Retrieve single message

```gherkin
Given a System Administrator user is authenticated
And a system message exists with ID "550e8400-e29b-41d4-a716-446655440000"
When GET /api/v1/system-messages/550e8400-e29b-41d4-a716-446655440000 is called
Then the API returns status 200
And the response includes all message fields
And the message data matches the stored values
And no data is persisted
```

### Scenario 5: Retrieve message not found

```gherkin
Given a System Administrator user is authenticated
When GET /api/v1/system-messages/00000000-0000-0000-0000-000000000000 is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the message ID that was not found
And no data is persisted
```

### Scenario 6: List messages with pagination

```gherkin
Given a System Administrator user is authenticated
And 25 system messages exist in the database
When GET /api/v1/system-messages?skip=0&limit=10 is called
Then the API returns status 200
And the response includes an items array with 10 messages
And the total field is 25
And the skip field is 0
And the limit field is 10
And messages are sorted by createdDate descending (most recent first)
And no data is persisted
```

### Scenario 7: List messages with priority filter

```gherkin
Given a System Administrator user is authenticated
And system messages exist with mixed priorities (standard and important)
When GET /api/v1/system-messages?priority=important is called
Then the API returns status 200
And the response includes only messages with priority "important"
And no data is persisted
```

### Scenario 8: List messages with status filter

```gherkin
Given a System Administrator user is authenticated
And system messages exist with different statuses
When GET /api/v1/system-messages?status=active is called
Then the API returns status 200
And the response includes only messages with status "active"
And no data is persisted
```

### Scenario 9: Update message with valid data

```gherkin
Given a System Administrator user is authenticated
And a system message exists with ID "550e8400-e29b-41d4-a716-446655440000"
And the current message has title "COVID-19 Testing Service"
When PUT /api/v1/system-messages/550e8400-e29b-41d4-a716-446655440000 is called with:
- title: "COVID-19 Testing Service - Updated"
- summary: "New COVID testing location - Check hours"
- messageBody: "<p>Testing location is now open 09:00-17:00</p>"
- priority: "important"
- startDateTime: "2026-06-10T09:00:00Z"
- expiryDateTime: "2026-06-30T17:00:00Z"
- visibleRoles: ["cas-pharmacist-admin", "cas-pharmacist"]
Then the API returns status 200
And the response includes all updated fields
And the title is updated to "COVID-19 Testing Service - Updated"
And the lastUpdatedBy field contains the authenticated user ID
And the lastUpdatedDateTime is set to the current UTC time
And the createdBy and createdDateTime remain unchanged
And the message is persisted in the database with new values
```

### Scenario 10: Update message not found

```gherkin
Given a System Administrator user is authenticated
When PUT /api/v1/system-messages/00000000-0000-0000-0000-000000000000 is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains "System message with ID 00000000-0000-0000-0000-000000000000 not found"
And no data is persisted
```

### Scenario 11: Delete message successfully

```gherkin
Given a System Administrator user is authenticated
And a system message exists with ID "550e8400-e29b-41d4-a716-446655440000"
When DELETE /api/v1/system-messages/550e8400-e29b-41d4-a716-446655440000 is called
Then the API returns status 204
And the response body is empty
And the message is marked as deleted in the database
And the deletedBy field contains the authenticated user ID
And the deletedDateTime is set to the current UTC time
And the status is automatically calculated as "deleted"
And the message is no longer returned in GET /api/v1/system-messages
```

### Scenario 12: Delete message not found

```gherkin
Given a System Administrator user is authenticated
When DELETE /api/v1/system-messages/00000000-0000-0000-0000-000000000000 is called
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message contains the message ID that was not found
And no data is persisted
```

### Scenario 13: Missing required field validation

```gherkin
Given a valid bearer token for a System Administrator user
When POST /api/v1/system-messages is called with a request missing the title field
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "title": ["Title is required"] }
And no data is persisted
```

### Scenario 14: Invalid date range validation

```gherkin
Given a valid bearer token for a System Administrator user
When POST /api/v1/system-messages is called with:
- startDateTime: "2026-06-15T17:00:00Z"
- expiryDateTime: "2026-06-10T09:00:00Z"
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "expiryDateTime": ["Expiry date must be after the start date"] }
And no data is persisted
```

### Scenario 15: Title exceeds character limit

```gherkin
Given a valid bearer token for a System Administrator user
When POST /api/v1/system-messages is called with a title containing 250 characters
Then the API returns status 400
And the response type is "ValidationException"
And the errors object contains: { "title": ["Title must be 200 characters or less"] }
And no data is persisted
```

### Scenario 16: Unauthorized user cannot manage messages

```gherkin
Given a bearer token for a user without System Administrator role
When POST /api/v1/system-messages is called with valid message data
Then the API returns status 403
And the response type is "UnauthorizedAccessException"
And the detail message is "User does not have permission to manage system messages. Only System Administrators can access this endpoint."
And no data is persisted
```

---

## Related Standards

- [standards/API-PATH-CONVENTIONS.md](../standards/API-PATH-CONVENTIONS.md) — URL structure and naming
- [standards/API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md) — Response format standards
- [standards/API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) — Error response structure
- [standards/API-DOMAIN-TYPES-AND-CODE-SYSTEMS.md](../standards/API-DOMAIN-TYPES-AND-CODE-SYSTEMS.md) — Code systems
- [guides/GHERKIN-FORMAT.md](../guides/GHERKIN-FORMAT.md) — Acceptance criteria formatting

---

## Linked Stories

**Related Stories:**
- [617546: System Message Management Feature](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617546) — Parent feature defining business requirements
- [617590: UI: Message Management Add/Edit - Create and Format System Messages](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/617590) — UI story that consumes this API

---

## Acceptance Notes

- All responses must follow ASP.NET Core `ProblemDetails` format for errors
- All timestamps must be in UTC ISO 8601 format
- All IDs must be UUIDs
- Audit trail fields (createdBy, createdDateTime, etc.) are set by the backend and not accepted in request bodies
- Message status is calculated automatically; it is not set by the client
- HTML content sanitization must prevent XSS attacks
- All database operations must be transactional
- Role-based visibility filtering occurs at retrieval time
