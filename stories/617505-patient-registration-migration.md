# Patient Registration Migration UI

## Story Type: UI

---

## Description

### Story Card
**As a** system administrator  
**I want** to initiate and track patient registration migrations between pharmacy accounts following ownership changes reported by NWSSP  
**So that** I can ensure accurate patient records are transferred to the new pharmacy account and maintain a complete audit trail for compliance and operational accuracy

### Context
When NWSSP (NHS Wales Shared Services Partnership) reports a pharmacy ownership change via support call, system administrators must migrate all patient registrations from the old pharmacy account to the new one.

**NWSSP Background:** See [`APPLICATION-CONTEXT.md`](../APPLICATION-CONTEXT.md#-nwssp-nhs-wales-shared-services-partnership) for detailed information about NHS Wales Shared Services Partnership and its role in managing pharmacy network changes.

This page provides a centralized administrative interface for system administrators to:
- Initiate new patient registration migrations by providing current (old) and new account details, along with the SP Call ID from NWSSP
- View historical migration records with patient counts and operation status
- Track audit trails for compliance and operational accountability

This feature supports the operational requirement for accurate patient data transfer during pharmacy ownership transitions in the NHS Wales Choose Pharmacy system.

### Out of Scope
- Automatic migration batch processing (manual initiation only in this story)
- Migration rollback or undo functionality
- Patient-level migration selection or filtering (all patients migrate together)
- CSV/bulk upload of migration data
- Advanced reporting or analytics on migration patterns
- Integration with external pharmacy management systems

### Related/Dependent Stories
- [617506]: Patient Registration Migration - API Backend — blocks this story; provides the service endpoints
- [617507]: Patient Registration Migration - Data Validation — relates to; handles validation rules

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

**Components used in this story:**
- [Tabs](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6165-80) — Switch between New Migration and Migration History
- [Text Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6219-549) — Current Account Number, New Account Number, SP Call ID
- [Date Input](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4583) — Date and Time of migration
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Discard Changes (secondary), Complete Migration (primary)
- [Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589) — Purpose and guidance text
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Migration History with sortable columns
- [Pagination](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9568) — Navigate through Migration History records
- [Tag](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-78) — Status badge (Success)
- [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4585) — Display validation errors for form fields
- [Important Information](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=7785-6796) — Alert about migration purpose

---

## Acceptance Criteria

**Total Scenarios: 8**

### Page Structure & Content

**Scenario 1: Patient Registration Migration page displays with Purpose section and two tabs**

```gherkin
Given a system administrator navigates to the Patient Registration Migration page
When the page loads
Then the page title displays: "Patient Registration Migration"
And an Important Information box is displayed with:
  - Title: "Purpose"
  - Text: "This page allows system administrators to initiate and complete patient registration migration for a pharmacy ownership change following NWSSP notification. The function provides a full audit trail and accurate reporting of patient registration transfers."
And two tabs are displayed:
  - "Migration History" (currently inactive, styled secondary)
  - "New Migration" (currently active, styled primary/blue)
And the active tab content displays below the tab bar
```

### Default State

**Scenario 2: New Migration form displays in default state with all fields empty**

```gherkin
Given the system administrator is on the Patient Registration Migration page
When the "New Migration" tab is displayed (page load or tab click)
Then the "Migration Details" section is displayed with:
  - "Current Account Number (Starts with 60)" text input (empty, required field marked with *)
  - "New Account Number (Starts with 60)" text input (empty, required field marked with *)
  - "Date and Time of migration" date/time input (empty, required field marked with *, showing current date/time placeholder: "16-Oct-2026 09:24")
  - "SP Call ID" text input (empty, optional field, no asterisk)
And the form footer displays two buttons:
  - "Discard Changes" button (secondary style, red/orange background)
  - "Complete Migration" button (primary style, blue background)
And all input fields are enabled and ready for administrator input
```

**Scenario 3: Migration History tab displays table with sorting and pagination**

```gherkin
Given the system administrator is on the Patient Registration Migration page
When the user clicks the "Migration History" tab
Then the tab becomes active (styled primary/blue)
And a table is displayed with the following columns:
  - Date (sortable, with ⇅ sort indicator)
  - Current Account Number (text, left-aligned)
  - New Account Number (text, left-aligned)
  - Call ID (clickable links, reference to NWSSP support call)
  - Patient Count (numeric, right-aligned)
  - Status (badge, showing "Success" in green tag)
And the table displays up to 10 rows per page
And pagination controls are displayed at the bottom: "First Previous 1 2 Next Last"
And the table is sorted by Date in descending order by default
And data rows display the migration history in chronological order (newest first)
```

### User Interaction

**Scenario 4: Administrator enters valid account numbers and timestamp, form captures input correctly**

```gherkin
Given the system administrator is on the "New Migration" tab
When the administrator enters the following data (from NWSSP support call):
  - Current Account Number: "604067A"
  - New Account Number: "987654A"
  - Date and Time: "16-Oct-2026 09:24"
  - SP Call ID: "9999132"
Then each field displays the entered value
And the form retains the values if the administrator moves focus between fields
And the values are formatted correctly:
  - Account numbers display in uppercase (if entered in lowercase)
  - Date/time displays in DD-MMM-YYYY HH:MM format
And the Discard Changes button is enabled (clickable)
```

**Scenario 5: Administrator clicks Discard Changes button clears form without submission**

```gherkin
Given the system administrator has entered data in the New Migration form:
  - Current Account Number: "604067A"
  - New Account Number: "987654A"
  - Date and Time: "16-Oct-2026 09:24"
  - SP Call ID: "9999132"
When the administrator clicks the "Discard Changes" button
Then a confirmation dialog appears (or inline confirmation):
  - Text: "Are you sure you want to discard your changes?"
  - Two buttons: "Cancel" and "Discard"
And if the administrator clicks "Discard":
  - All form fields are cleared (return to empty state)
  - The page remains on the "New Migration" tab
  - No data is persisted
And if the administrator clicks "Cancel":
  - The dialog closes
  - The entered data is retained in the form
```

**Scenario 6: Administrator switches between tabs and form data is preserved**

```gherkin
Given the system administrator is on the "New Migration" tab
And has entered data from NWSSP support call:
  - Current Account Number: "604067A"
  - New Account Number: "987654A"
When the administrator clicks the "Migration History" tab
Then the Migration History table is displayed
And the previously entered form data is retained in memory
When the administrator clicks back to the "New Migration" tab
Then all previously entered data is displayed:
  - Current Account Number: "604067A"
  - New Account Number: "987654A"
  - Date and Time: "16-Oct-2026 09:24"
  - SP Call ID: "9999132"
```

### Navigation

**Scenario 7: Administrator clicks Complete Migration button submits form and displays success message**

```gherkin
Given the system administrator is on the "New Migration" tab
And has entered all required fields from the NWSSP support call:
  - Current Account Number: "604067A"
  - New Account Number: "987654A"
  - Date and Time: "16-Oct-2026 09:24"
When the administrator clicks the "Complete Migration" button
Then the form is submitted to the backend API
And a success message is displayed:
  - Type: Notification Banner (green success style)
  - Text: "Migration completed successfully"
  - Duration: Persists on page (does not auto-dismiss)
And the form fields are cleared to empty state
And the administrator remains on the "New Migration" tab
And the migration record appears in the "Migration History" tab
And the administrator can initiate another migration immediately
```

### Validation

**Scenario 8: Administrator submits form with missing required fields displays validation errors**

```gherkin
Given the system administrator is on the "New Migration" tab
When the administrator leaves the required fields empty and clicks "Complete Migration"
Then validation errors are displayed in an Error Summary at the top of the form with:
  - "Enter Current Account Number"
  - "Enter New Account Number"
  - "Enter Date and Time of migration"
And individual error messages appear inline near each field:
  - Below the Current Account Number field: "Enter Current Account Number" (red text, warning icon)
  - Below the New Account Number field: "Enter New Account Number" (red text, warning icon)
  - Below the Date and Time field: "Enter Date and Time of migration" (red text, warning icon)
And each error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic content announcement
And focus moves to the first field with an error (Current Account Number)
And the form remains on the "New Migration" tab
And no data is persisted
And when the administrator enters valid data in any field, that field's error message disappears
```

### Accessibility

**Scenario 9: Page is navigable via keyboard with logical tab order and screen reader support**

```gherkin
Given the Patient Registration Migration page is rendered
When a system administrator navigates using the Tab key from the top of the page
Then the keyboard focus order follows this sequence:
  - "New Migration" tab
  - "Migration History" tab
  - Current Account Number field
  - New Account Number field
  - Date and Time field
  - SP Call ID field
  - "Discard Changes" button
  - "Complete Migration" button
And when the administrator presses Shift+Tab, focus moves backward in reverse order
And all interactive elements are visibly focused with a clear focus indicator (outline/border)
And when the Migration History tab is active, focus order includes:
  - Table column headers (for sorting)
  - Pagination controls (First, Previous, 1, 2, Next, Last)
  - Clickable Call ID links in table rows
And screen readers announce:
  - Page title: "Patient Registration Migration"
  - Tab labels: "New Migration" and "Migration History" with state (selected/not selected)
  - Form field labels: "Current Account Number (Starts with 60)", "New Account Number (Starts with 60)", "Date and Time of migration", "SP Call ID"
  - Required field indicators: "required" or "This field is required"
  - Button labels: "Discard Changes" and "Complete Migration"
  - Button roles: "button"
  - Table structure: number of rows, columns, column headers
And colour contrast meets WCAG 2.1 AA standard (minimum 4.5:1 for text)
And all form inputs have associated labels (aria-label or <label> elements)
And error messages are announced immediately when displayed
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage of form logic and tab switching)
- [ ] Integration tests written (form submission, validation, error handling)
- [ ] Accessibility testing completed (WCAG 2.1 AA) — tested with screen readers (NVDA, JAWS) and keyboard navigation
- [ ] UI matches DHCW Design System V2 (Figma components, spacing, colours)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (form remains usable on tablet/mobile screens)
- [ ] Form validation works offline and with connectivity issues handled gracefully
- [ ] Date/time picker works across all browsers and devices
- [ ] Table sorting and pagination functions correctly
- [ ] Unsaved form changes warning implemented (optional: confirm before leaving page)
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes
- Date/time format should allow both manual entry and picker widget for accessibility
- SP Call ID should be optional but recommended (reference to NWSSP support call for audit trail)
- Account numbers should be validated server-side to ensure they start with "60" and that accounts exist
- Migration History table should refresh automatically or include a "Refresh" button to show newly completed migrations
- Consider adding a warning banner if the system administrator attempts to migrate accounts that have active patients to prevent data loss
- Consider adding filters (by date range, status, account number) in future iterations for large datasets with many migration records
- Migration operations should be logged with the administrator's user ID and timestamp for audit compliance
- Consider read-only access mode for system administrators with audit-only permissions (view history but cannot initiate migrations)
