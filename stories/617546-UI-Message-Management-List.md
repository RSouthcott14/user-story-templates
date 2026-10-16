# UI User Story: System Administration - Message Management List

## Story Type: UI

---

## Description

### Story Card
**As a** Choose Pharmacy system administrator  
**I want** to view and manage all system messages in a centralized list  
**So that** I can maintain message content, control message visibility, and manage the message lifecycle across the platform

### Context
Choose Pharmacy NextGen uses system messages to communicate important information to users, such as service announcements, system maintenance notifications, and role-specific alerts. System administrators must be able to view all existing messages, filter by status or role visibility, and manage message content without technical intervention. 

The message management list provides:
- **Message list display** — All system messages in a table with key details
- **Message attributes** — Title, Priority, Summary, Start Date, End Date, Visible to Roles
- **Action capabilities** — Edit, delete, or view message details
- **Add message button** — Trigger creation of new messages
- **Search and filtering** — Find messages by title or other attributes
- **Status indicators** — Visual indicators for active/inactive/archived messages
- **Responsive design** — Works seamlessly on desktop and tablet devices

### Out of Scope
- Bulk operations (delete/archive multiple messages at once)
- Message scheduling/scheduling UI (dates are set in Add Message story)
- Message versioning or history tracking
- Export functionality (CSV, Excel, PDF)
- Advanced search filters or saved filters
- Message template library
- Approval workflows for message publication
- Real-time message refresh or live updates
- API documentation or technical configuration

### Related/Dependent Stories
- [617546-UI-Message-Management-Add](./617546-UI-Message-Management-Add.md) — Add new message screen (linked via "Add message" button)
- [Message API] — Backend API for message management (separate story)
- [Authentication & Authorization] — Ensures only admins access this area

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Components used in this story:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with branding
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Messages list in structured tabular format
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Add message button (primary) and action buttons (edit/delete/view)
- [Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6219-549) — Search field for finding messages
- [Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391) — Filter dropdown (by Priority or Status)
- [Tag](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-78) — Status badges and priority badges
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Section containers
- [Pagination](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9568) — Navigate through pages of messages
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Mock-up:** `mock-designs/message-management-list-mock.html`

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Message management list displays with full table structure**

```gherkin
Given the user navigates to System Administration > Message Management
When the page loads
Then the following elements are displayed:
  - Page header: "Message List" with subtitle "Manage system messages and announcements"
  - Page title: "All Messages"
  - Primary button labeled "Add new message" positioned top-right
  - Search field labeled "Search messages by title"
  - Filter dropdown labeled "Filter by Priority" with options: All, Normal, Important, Critical
  - Table header row with columns:
    - Title (sortable)
    - Priority (sortable)
    - Summary (sortable)
    - Start Date (sortable)
    - End Date (sortable)
    - Visible to (sortable)
    - Actions column
  - Table data rows populated with message records
  - Pagination component showing "Showing 1-10 of [total] messages"
  - Footer with application information
And each table row displays:
  - Message title (text, left-aligned)
  - Priority badge with color coding:
    - "Normal" with neutral styling (gray)
    - "Important" with warning styling (orange/yellow)
    - "Critical" with alert styling (red)
  - Summary text (truncated to 60 characters with ellipsis)
  - Start Date in format "DD MMM YYYY HH:MM" (e.g., "10 Jun 2026 00:00")
  - End Date in format "DD MMM YYYY HH:MM" (e.g., "11 Jun 2026 00:00")
  - Role badges: "CAS Pharmacist Admin", "CAS Pharmacist" (multiple badges if applicable)
  - Action buttons: Edit (pencil icon), Delete (trash icon), View Details (eye icon)
And the table has alternating row background colors for readability (white/light gray)
And the layout is responsive with columns collapsing on smaller screens (tablet view)
```

**Scenario 2: Empty message list displays helpful guidance**

```gherkin
Given the user navigates to Message Management
When the system has zero messages
Then instead of a table, the page displays:
  - Large centered icon (empty state)
  - Heading: "No messages yet"
  - Message text: "Create your first system message to get started"
  - "Add new message" button (primary)
And the search and filter controls are still visible but disabled/grayed out
And pagination is not displayed
```

### Default State

**Scenario 3: Message list loads with all messages and default filter**

```gherkin
Given the user navigates to Message Management
When the page loads for the first time
Then the page displays all system messages (no filter applied)
And "Filter by Priority" dropdown shows "All" as selected
And the search field is empty
And the first 10 messages are displayed (if more than 10 exist)
And pagination shows "Showing 1-10 of [total] messages"
And the table is sorted by Start Date in descending order (newest first)
And all "Add new message", Edit, Delete, and View Details buttons are visible and enabled
```

### User Interaction

**Scenario 4: User searches for messages by title**

```gherkin
Given the user is on the Message Management list
And the search field is empty
When the user types "vaccination" in the search field
Then the table updates in real-time to show only messages containing "vaccination" in the title
And the search results persist as the user types
And the pagination updates to show the count of matching messages
When the user clears the search field (deletes all text)
Then all messages are displayed again
And the table returns to the default sorted order
When the user searches for "nonexistent message"
Then the table displays empty state: "No messages match your search"
And the "Add new message" button remains visible
```

**Scenario 5: User filters messages by priority level**

```gherkin
Given the user is on the Message Management list
When the user clicks "Filter by Priority" dropdown
Then the dropdown expands showing options:
  - All (currently selected)
  - Normal
  - Important
  - Critical
When the user selects "Important"
Then the dropdown closes
And the table updates to show only messages with "Important" priority
And the Priority column highlights the selected filter value
And pagination updates to show the count of "Important" messages
When the user selects "Critical"
Then the table updates to show only "Critical" priority messages
When the user re-opens the dropdown and selects "All"
Then all messages are displayed again
And the table returns to displaying all priorities
```

**Scenario 6: User sorts table columns by clicking column headers**

```gherkin
Given the user is on the Message Management list
When the user clicks the "Title" column header
Then the table re-sorts alphabetically by Title (A-Z)
And a sort indicator (↑) appears next to "Title"
When the user clicks "Title" again
Then the table re-sorts in reverse order (Z-A)
And the sort indicator changes to (↓)
When the user clicks "Start Date" column header
Then the table sorts by Start Date in ascending order (oldest first)
And the sort indicator appears next to "Start Date"
And the "Title" column header sort indicator disappears
When the user clicks "Priority" column header
Then the table sorts by Priority (Normal → Important → Critical)
And appropriate sort indicators are displayed
```

**Scenario 7: User edits a message by clicking Edit button**

```gherkin
Given the user is on the Message Management list
And the user can see a message row with an Edit button (pencil icon)
When the user clicks the Edit button
Then the user is navigated to the "Edit Message" screen
And the message form is populated with the selected message's data:
  - Message Title field shows the current title
  - Priority dropdown shows the current priority
  - Summary field shows the current summary
  - Start Date field shows the current start date
  - End Date field shows the current end date
  - Visible to Roles checkboxes show the current role selections
  - Message Content WYSIWYG shows the current formatted content
And the page title changes to "Edit Message: [Message Title]"
And a "Cancel" button returns the user to the Message Management list without changes
```

**Scenario 8: User deletes a message with confirmation**

```gherkin
Given the user is on the Message Management list
And the user can see a message row with a Delete button (trash icon)
When the user clicks the Delete button
Then a confirmation dialog appears with:
  - Title: "Delete message?"
  - Message text: "Are you sure you want to delete '[Message Title]'? This action cannot be undone."
  - "Cancel" button (secondary)
  - "Delete" button (destructive/red styling)
When the user clicks "Cancel"
Then the dialog closes
And the message remains in the list
And no data is deleted
When the user clicks the Delete button again and confirms deletion
Then a success message appears: "Message '[Message Title]' was successfully deleted"
And the message is removed from the table
And pagination count updates
And the user remains on the Message Management list
```

**Scenario 9: User views full message details**

```gherkin
Given the user is on the Message Management list
And the user can see a message row with a View Details button (eye icon)
When the user clicks the View Details button
Then a modal or detail panel opens displaying:
  - Full message title
  - Priority with color badge
  - Full summary text (not truncated)
  - Start Date and Time
  - End Date and Time
  - Full Message Content in read-only format (with formatting preserved)
  - List of visible roles (all roles the message is visible to)
  - Status indicator (Active/Inactive/Expired)
  - Created/Updated timestamps
  - "Close" or back button to return to the list
And the Message Management list remains visible behind the modal/panel
And no editing is possible in this view (read-only)
```

### Navigation

**Scenario 10: User navigates to Add Message from the list**

```gherkin
Given the user is on the Message Management list
When the user clicks the "Add new message" button
Then the user is navigated to the "Add New Message" screen
And the form displays empty fields:
  - Message Title field is empty
  - Priority dropdown shows "Please Select"
  - Summary field is empty
  - Start Date field is empty
  - End Date field is empty
  - Visible to Roles checkboxes are all unchecked
  - Message Content WYSIWYG is empty
And the page title shows "Add New Message"
And a "Cancel" button returns the user to the Message Management list
And any data entered in the form is discarded if the user cancels
```

### Validation

**Scenario 11: Validation error handling during search/filter operations**

```gherkin
Given the user is on the Message Management list
When the backend fails to load message data (API error)
Then an error message is displayed:
  - Error banner at the top of the page with role="alert"
  - Message text: "Unable to load messages. Please try again later."
  - Icon: warning/alert icon
  - Styling: red/error color from design system
And the search and filter controls remain visible but functional
And the table displays "Loading failed" state
And a "Retry" button allows the user to attempt reloading
And focus moves to the error message for screen reader announcement
And no data is persisted on error
When the user clicks "Retry"
Then the page attempts to reload the message list again
```

### Accessibility

**Scenario 12: WCAG 2.1 AA keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using only a keyboard
Then all interactive elements are reachable via Tab key:
  - "Add new message" button
  - Search field
  - Filter Priority dropdown
  - Edit, Delete, View Details buttons for each message row
  - Pagination links/buttons
And the tab order is logical from top to bottom:
  1. "Add new message" button
  2. Search field
  3. Filter dropdown
  4. Table (first row, first actionable item)
  5. Action buttons (edit → delete → view details)
  6. Next row...
  7. Pagination
And the search field has aria-label="Search messages by title"
And the filter dropdown has aria-label="Filter messages by priority level"
And table has role="table" with appropriate headers
And each table header has scope="col" attribute
And sortable columns have aria-sort="ascending" or "descending" or "none"
And priority badges have aria-label describing severity (e.g., "Important priority")
And action buttons have aria-label describing action: "Edit message", "Delete message", "View details"
And screen reader announces:
  - "Message List, heading level 1" when entering the page
  - "Search field" when focused
  - "Filter dropdown, currently showing All" when focused
  - "Table of messages, [X] rows" when table is focused
  - Each table row as: "[Title] [Priority] [Summary] [Start Date] [End Date] [Roles]" with action buttons announced separately
And colour contrast ratios meet WCAG AA standard (4.5:1 for normal text, 3:1 for large text)
  - Table header text (#1a1a1a) against light gray background (#f5f5f5): 14:1 ✓
  - Priority badge text (various) against badge background: all meet 4.5:1 minimum ✓
And all buttons have visible focus indicators (outline or background change)
And the table remains accessible when columns are hidden/reordered on smaller screens
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Integration tests for message list API endpoints
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches DHCW Design System V2 (Figma/mock-up provided)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Responsive design tested (tablet: 768px, desktop: 1200px+)
- [ ] Search functionality tested with various message titles
- [ ] Filter by Priority works correctly (All, Normal, Important, Critical)
- [ ] Sorting on all sortable columns works correctly
- [ ] Edit navigation passes message ID to edit form
- [ ] Delete confirmation dialog works and removes message
- [ ] View Details shows all message attributes correctly
- [ ] Add Message button navigates to [617546-UI-Message-Management-Add]
- [ ] Empty state displays when no messages exist
- [ ] Pagination displays and navigates correctly
- [ ] Error states handled gracefully with user-friendly messages
- [ ] No console errors or warnings
- [ ] Keyboard navigation tested end-to-end
- [ ] Screen reader testing completed (NVDA/JAWS on Windows, VoiceOver on macOS)
- [ ] Performance: Message list loads in under 2 seconds
- [ ] Mock-up artifact (`mock-designs/message-management-list-mock.html`) provided as reference

---

## Notes

### Design Reference
- Mock-up provided: `mock-designs/message-management-list-mock.html`
- DHCW Design System V2 Figma: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

### Data Considerations
- Table displays up to 10 messages per page by default (configurable)
- Messages sorted by Start Date (descending - newest first) by default
- Search is case-insensitive and searches Title field
- Priority filter applies OR logic (show messages matching selected priority)
- Pagination shows current page and total message count
- Status can be derived from current date vs Start/End dates:
  - Active: Current date is between Start and End date
  - Upcoming: Current date is before Start date
  - Expired: Current date is after End date

### Message Attributes
- Title: Required, string, max 200 characters
- Priority: Required, enum (Normal, Important, Critical)
- Summary: Required, string, max 500 characters
- Start Date: Required, datetime
- End Date: Required, datetime
- Visible to Roles: Required, multi-select checkboxes (CAS Pharmacist Admin, CAS Pharmacist)
- Message Content: Required, rich text with formatting (WYSIWYG editor)
- Status: Derived (Active/Inactive/Expired)
- Created: Timestamp
- Updated: Timestamp

### Future Enhancements (Not in scope)
- Bulk operations (delete multiple messages)
- Message versioning/history
- Export to CSV/Excel/PDF
- Advanced filters (by date range, by created user)
- Message templates
- Approval workflow
- Real-time updates (live message refresh)
- Message scheduling/calendar view
- Duplicate message functionality

### Known Issues / Dependencies
- Requires authentication to ensure only system admins can access this page
- Backend API must support:
  - List all messages with pagination
  - Search/filter messages
  - Sort by any column
  - Update message (for edit)
  - Delete message (with confirmation)
  - Retrieve single message details
