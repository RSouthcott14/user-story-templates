# Messages API

## Story Type: API

---

## Description

### Story Card
**As a** pharmacy user interface  
**I need** a Messages API that retrieves and manages system administrator and alert messages  
**So that** pharmacy staff can view, filter, and interact with critical communications without backend delays

### Context
The Messages API provides the backend services for the Messages screen, delivering messages to pharmacy users with efficient filtering, pagination, and read/unread status management. This API supports two message types:
- **System Administrator Messages:** Routine updates, maintenance notifications, and policy changes
- **Alert Messages:** Time-sensitive warnings about medication safety, drug interactions, and clinical alerts

The API is accessed exclusively by the Messages screen UI and must support responsive pagination for high-volume message lists.

### Out of Scope
- Patient messages and consultation follow-ups (future phase)
- Referral management (future phase)
- Real-time message push notifications (handled by notification service)
- Message composition/sending (separate story)
- Message search functionality (future enhancement)
- Message archiving and permanent deletion
- Email delivery of messages
- Third-party messaging integration

---

## Endpoints

### 1. Get Messages List

**HTTP Method & Path:**
```
GET /api/v1/messages
```

**Purpose:** Retrieve a paginated list of messages with optional filtering by message type.

**Query Parameters:**

| Parameter | Type | Required | Default | Constraints | Purpose |
|-----------|------|----------|---------|-------------|---------|
| `type` | string | No | `null` | `system`, `alert`, or omitted for all | Filter by message type |
| `skip` | integer | No | `0` | ≥ 0 | Number of results to skip (pagination offset) |
| `limit` | integer | No | `10` | 1–100 | Maximum results per page |

**Request Example:**
```
GET /api/v1/messages?type=alert&skip=0&limit=10
```

**Success Response (200 OK):**
```json
{
  "items": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "type": "alert",
      "senderName": "System Alert",
      "subject": "Drug interaction warning for patient consultation",
      "preview": "A potential drug interaction has been detected during a pharmacy consultation...",
      "timestamp": "2026-10-23T13:45:00Z",
      "isRead": false,
      "priority": "high"
    },
    {
      "id": "550e8400-e29b-41d4-a716-446655440001",
      "type": "system",
      "senderName": "System Administrator",
      "subject": "System Maintenance Scheduled",
      "preview": "Scheduled maintenance will occur on Saturday, October 26 at 22:00 GMT...",
      "timestamp": "2026-10-23T09:30:00Z",
      "isRead": true,
      "priority": "medium"
    }
  ],
  "total": 47,
  "skip": 0,
  "limit": 10
}
```

**Response Fields:**
- `id`: [GUID] — Unique message identifier
- `type`: [string] — Message type: `system` or `alert`
- `senderName`: [string] — Display name of sender (e.g., "System Administrator", "System Alert")
- `subject`: [string] — Message subject line
- `preview`: [string] — First 150–200 characters of message body for preview
- `timestamp`: [ISO 8601 datetime] — When the message was created (UTC)
- `isRead`: [boolean] — Whether the user has read this message
- `priority`: [string] — Message priority level: `low`, `medium`, or `high`
- `total`: [integer] — Total count of all messages matching filter
- `skip`: [integer] — Offset of results (from request)
- `limit`: [integer] — Maximum results per page (from request)

---

### 2. Get Single Message

**HTTP Method & Path:**
```
GET /api/v1/messages/{messageId}
```

**Purpose:** Retrieve full details of a single message by ID.

**Path Parameters:**

| Parameter | Type | Required | Constraints |
|-----------|------|----------|-------------|
| `messageId` | GUID | Yes | Must be a valid GUID |

**Request Example:**
```
GET /api/v1/messages/550e8400-e29b-41d4-a716-446655440000
```

**Success Response (200 OK):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "type": "alert",
  "senderName": "System Alert",
  "senderRole": "Automated System",
  "subject": "Drug interaction warning for patient consultation",
  "body": "A potential drug interaction has been detected during a pharmacy consultation. The medications involved are: Warfarin (patient current medication) and Aspirin (prescribed in consultation). Please review for contraindications before proceeding with the prescription. Contact your clinical governance team if you need further guidance.",
  "timestamp": "2026-10-23T13:45:00Z",
  "isRead": true,
  "priority": "high",
  "actionUrl": null
}
```

**Response Fields:**
- `id`: [GUID] — Unique message identifier
- `type`: [string] — Message type: `system` or `alert`
- `senderName`: [string] — Display name of sender
- `senderRole`: [string] — Role/department of sender
- `subject`: [string] — Message subject
- `body`: [string] — Full message content (plain text or markdown)
- `timestamp`: [ISO 8601 datetime] — When message was created (UTC)
- `isRead`: [boolean] — Read status
- `priority`: [string] — Priority level: `low`, `medium`, or `high`
- `actionUrl`: [string or null] — Optional URL for related action (e.g., link to affected consultation)

---

### 3. Mark Message as Read/Unread

**HTTP Method & Path:**
```
PATCH /api/v1/messages/{messageId}
```

**Purpose:** Update the read status of a message.

**Path Parameters:**

| Parameter | Type | Required | Constraints |
|-----------|------|----------|-------------|
| `messageId` | GUID | Yes | Must be a valid GUID |

**Request Body:**
```json
{
  "isRead": true
}
```

**Request Fields:**
- `isRead`: [boolean] — `true` to mark as read, `false` to mark as unread

**Success Response (200 OK):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "isRead": true,
  "timestamp": "2026-10-23T13:45:00Z"
}
```

**Response Fields:**
- `id`: [GUID] — The message ID
- `isRead`: [boolean] — Updated read status
- `timestamp`: [ISO 8601 datetime] — Last modified timestamp

---

### 4. Get Unread Count

**HTTP Method & Path:**
```
GET /api/v1/messages/unread-count
```

**Purpose:** Retrieve the count of unread messages.

**Query Parameters:**

| Parameter | Type | Required | Default | Purpose |
|-----------|------|----------|---------|---------|
| `type` | string | No | `null` | Filter by type: `system`, `alert`, or all if omitted |

**Request Example:**
```
GET /api/v1/messages/unread-count
GET /api/v1/messages/unread-count?type=alert
```

**Success Response (200 OK):**
```json
{
  "total": 5,
  "byType": {
    "system": 2,
    "alert": 3
  }
}
```

**Response Fields:**
- `total`: [integer] — Total unread messages across all types
- `byType`: [object] — Count of unread messages by type
  - `system`: [integer] — Unread system messages
  - `alert`: [integer] — Unread alert messages

---

## Acceptance Criteria

**Total Scenarios: 11**

### Endpoint 1: Get Messages List

**Scenario 1: Retrieve all messages with default pagination**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages is called with no query parameters
Then the API returns status 200
And the response contains a paginated list of messages
And the response includes items array with message objects
And each message includes: id, type, senderName, subject, preview, timestamp, isRead, priority
And the response includes pagination metadata: total, skip, limit
And the default skip is 0
And the default limit is 10
And messages are sorted by timestamp (most recent first)
And the response type is application/json
And messages of both types (system, alert) are included
```

**Scenario 2: Filter messages by type (alerts only)**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages?type=alert is called
Then the API returns status 200
And the items array contains only messages where type equals "alert"
And the total count reflects only alert messages
And no system messages are included
And the response includes correct pagination metadata for filtered results
```

**Scenario 3: Filter messages by type (system only)**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages?type=system is called
Then the API returns status 200
And the items array contains only messages where type equals "system"
And the total count reflects only system messages
And no alert messages are included
And the response includes correct pagination metadata for filtered results
```

**Scenario 4: Paginate messages using skip and limit**

```gherkin
Given a pharmacy user is authenticated
And there are 47 total messages
When GET /api/v1/messages?skip=10&limit=5 is called
Then the API returns status 200
And the items array contains exactly 5 messages
And the messages returned are items 11–15 (skipping the first 10)
And the response includes: total = 47, skip = 10, limit = 5
And the response does not include messages already returned in skip=0
```

**Scenario 5: Handle invalid limit parameter**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages?limit=150 is called (exceeding max of 100)
Then the API returns status 400
And the response type is "ValidationException"
And the detail message contains "Limit must be between 1 and 100"
And the errors object contains: { "limit": ["Limit must be between 1 and 100"] }
And no data is persisted
```

**Scenario 6: Handle invalid type parameter**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages?type=invalid is called
Then the API returns status 400
And the response type is "ValidationException"
And the detail message contains "Message type must be one of: system, alert"
And the errors object contains: { "type": ["Message type must be one of: system, alert"] }
And no data is persisted
```

### Endpoint 2: Get Single Message

**Scenario 7: Retrieve full details of a specific message**

```gherkin
Given a pharmacy user is authenticated
And a message with ID "550e8400-e29b-41d4-a716-446655440000" exists
When GET /api/v1/messages/550e8400-e29b-41d4-a716-446655440000 is called
Then the API returns status 200
And the response includes the complete message object
And the response includes all fields: id, type, senderName, senderRole, subject, body, timestamp, isRead, priority, actionUrl
And the message body contains the full text (not truncated)
And the isRead status is accurately reflected
```

**Scenario 8: Handle message not found**

```gherkin
Given a pharmacy user is authenticated
When GET /api/v1/messages/00000000-0000-0000-0000-000000000000 is called
And no message exists with that ID
Then the API returns status 404
And the response type is "EntityNotFoundException"
And the detail message is "Message with ID 00000000-0000-0000-0000-000000000000 not found"
And no data is persisted
```

### Endpoint 3: Mark Message as Read/Unread

**Scenario 9: Mark a message as read**

```gherkin
Given a pharmacy user is authenticated
And a message with ID "550e8400-e29b-41d4-a716-446655440000" exists with isRead = false
When PATCH /api/v1/messages/550e8400-e29b-41d4-a716-446655440000 is called with body { "isRead": true }
Then the API returns status 200
And the response includes: id, isRead = true, timestamp
And the message's isRead status is permanently updated in the database
And subsequent GET requests return isRead = true for this message
```

**Scenario 10: Mark a message as unread**

```gherkin
Given a pharmacy user is authenticated
And a message with ID "550e8400-e29b-41d4-a716-446655440000" exists with isRead = true
When PATCH /api/v1/messages/550e8400-e29b-41d4-a716-446655440000 is called with body { "isRead": false }
Then the API returns status 200
And the response includes: id, isRead = false, timestamp
And the message's isRead status is permanently updated in the database
And the unread count is recalculated
```

### Endpoint 4: Get Unread Count

**Scenario 11: Retrieve unread message counts**

```gherkin
Given a pharmacy user is authenticated
And the user has 5 unread messages total (2 system, 3 alert)
When GET /api/v1/messages/unread-count is called
Then the API returns status 200
And the response includes: total = 5
And the response includes byType: { "system": 2, "alert": 3 }
And filtering by type works correctly:
  - GET /api/v1/messages/unread-count?type=alert returns total = 3
  - GET /api/v1/messages/unread-count?type=system returns total = 2
And when all messages are marked as read:
  - The next call returns total = 0
```

---

## Security & Performance

### Authentication
- All endpoints require authentication (Bearer token in `Authorization` header)
- Unauthenticated requests return `401 Unauthorized`

### Authorization
- Users can only access their own messages (enforced by identity context)
- Attempting to access another user's message returns `403 Forbidden`

### Rate Limiting
- Implement standard API rate limits (TBD with API governance team)
- Return `429 Too Many Requests` if rate limit exceeded

### Performance Considerations
- Pagination defaults to `limit=10` to avoid large payloads
- Maximum `limit=100` to prevent abuse
- Database queries should use indexes on `timestamp`, `type`, and `isRead` fields
- Consider caching unread count (cache invalidation on PATCH)

---

## Response Format

All responses follow ASP.NET Core `ProblemDetails` format (see API-ERROR-HANDLING.md):

**Success:** `Content-Type: application/json`, `Cache-Control: private, no-cache`

**Errors:** `ProblemDetails` structure with `type`, `title`, `status`, `detail`, and `errors` (if validation)

---

## Related Standards

- [API-PATH-CONVENTIONS.md](../standards/API-PATH-CONVENTIONS.md) — Endpoint naming and structure
- [API-RESPONSE-PATTERNS.md](../standards/API-RESPONSE-PATTERNS.md) — Response format standards
- [API-ERROR-HANDLING.md](../standards/API-ERROR-HANDLING.md) — Error response structure

---

## Notes

- Message IDs are GUIDs; implementation must validate GUID format
- Timestamps are always in UTC (ISO 8601 format)
- Preview text is truncated to ~150–200 characters for list responses
- Message body supports plain text; HTML sanitization required on display (frontend responsibility)
- Read status is per-user (users don't share read state)
- Unread count is cached; cache invalidated on every PATCH operation
- Future phases may add search, archiving, and soft-delete support
