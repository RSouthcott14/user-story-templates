# Manage DMRs Page

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy professional  
**I want** to manage Discharge Medicines Review (DMR) consultations for a registered patient  
**So that** I can view historical DMR records, initiate new DMR consultations, and manage the patient's DMR service registration

### Context
This page forms the entry point for patients who are already registered for the Discharge Medicines Review (DMR) service. The page provides a summary of previously completed DMR consultations and enables the pharmacy professional to view, access, and manage DMR registrations.

The page is displayed only for patients who are already registered for DMR. Patients who are not registered will follow the DMR Registration pathway instead.

On page load, the system will perform an API call to:
1. Check whether the patient has an active DMR registration in the new application Service Registration table
2. If no active DMR registration exists in the new application, check the orphaned legacy Service Registration table
3. If no active DMR registration exists in either location, create a new CAS registration in the new application Service Registration table against the current pharmacy location
4. If an active DMR registration exists in the new application for a different pharmacy location, transfer the registration to the current pharmacy location
5. If no active DMR registration exists in the new application but an active DMR registration exists in the orphaned legacy Service Registration table:
   - Create a corresponding DMR registration in the new application Service Registration table using the legacy registration details
   - Associate the registration with the appropriate pharmacy location
   - Remove the migrated orphaned legacy record to prevent duplicate registrations and maintain data integrity

### Captions
**High-level caption:** Discharge Medicines Review  
**Page caption:** View and manage DMR consultations

### Out of Scope
- Detailed DMR consultation workflow and assessment logic (covered in separate stories)
- De-registration confirmation dialog or workflow (covered in separate story)
- New DMR consultation initiation and form submission (covered in separate story)
- DMR history filtering or sorting functionality (initial implementation shows all records)
- Export or print functionality for DMR records

### Related Stories
- **TBD:** DMR Consultation Detail Page — relates to: User clicks on DMR reference in table to view consultation details
- **TBD:** New DMR Initiation — relates to: "New DMR" button navigates to DMR consultation start page
- **TBD:** De-register Patient Confirmation — relates to: "De-register" button navigates to confirmation workflow

---

## Design Reference

**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Primary and secondary action buttons
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Display DMR history records
- [Notification Banners](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6610-4602) — Error and loading states
- [Action Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9493) — Clickable DMR reference links in table

---

## Acceptance Criteria

**Total Scenarios: 8**

### Page Structure & Content

**Scenario 1: Display page with DMR history on initial load**

```gherkin
Given a pharmacy professional is viewing a patient's DMR profile
And the patient has an active DMR registration
And the patient has historical DMR records
When the page loads
Then the following elements are displayed:
  - High-level caption: "Discharge Medicines Review"
  - Page heading: "Manage DMRs"
  - Primary button: "New DMR" with plus icon
  - Secondary button: "De-register patient from DMR service" (red outline style)
  - Table heading: "DMR reference" (left column)
  - Table heading: "Date" (center column)
  - Table heading: "Status" (right column)
  - At least one data row in the table
And the DMR reference column displays clickable links (blue underlined text)
And the Date column displays formatted dates (DD/MM/YYYY format)
And the Status column displays status badges with appropriate colors
And the "New DMR" button is styled as primary action (blue background)
And the "De-register" button is styled as secondary destructive action (red outline)
And the layout is responsive with appropriate spacing
```

**Scenario 2: Display page without DMR history**

```gherkin
Given a pharmacy professional is viewing a patient's DMR profile
And the patient has an active DMR registration
And the patient has no historical DMR records
When the page loads
Then the following elements are displayed:
  - High-level caption: "Discharge Medicines Review"
  - Page heading: "Manage DMRs"
  - Primary button: "New DMR" with plus icon
  - Secondary button: "De-register patient from DMR service"
And the DMR table is NOT displayed
And the page shows informational text: "No DMR records found. Start a new consultation using the 'New DMR' button."
And the "New DMR" button remains prominent and accessible
```

**Scenario 3: Display page during API registration check**

```gherkin
Given a pharmacy professional navigates to the DMR Manage page
When the page begins loading the API registration check
Then a loading indicator is displayed
And the message "Loading DMR information..." is shown
And interactive buttons are temporarily disabled
And the page waits for the API response before proceeding
```

### Default State

**Scenario 4: Page initialization with registration validation**

```gherkin
Given a pharmacy professional navigates to a patient's DMR management page
When the page loads and performs the registration check API call
Then the system checks for active DMR registration in the new application Service Registration table
And if no registration exists, checks the orphaned legacy Service Registration table
And if registration only exists in legacy, automatically migrates it to the new application
And if registration exists in a different pharmacy location, transfers it to the current pharmacy location
And the page displays the registration status (active/inactive) in a non-intrusive manner
And the "New DMR" button is enabled
And the "De-register" button is only enabled if an active registration exists
And the DMR history table loads with all available records
```

**Scenario 5: Display DMR history with multiple status types**

```gherkin
Given a pharmacy professional is viewing the Manage DMRs page
And the patient has multiple historical DMR records
When the page loads
Then the table displays all DMR records with:
  - DMR001 | 01/01/2026 | Completed (green badge)
  - DMR002 | 02/02/2026 | Completed (green badge)
  - DMR003 | 15/10/2025 | Completed (green badge)
And the high-level caption: "Discharge Medicines Review" is displayed at the top
And the page heading: "Manage DMRs" is displayed prominently
And the records are displayed in reverse chronological order (most recent first)
And each record row is visually distinct with appropriate spacing
And all DMR reference links are underlined and in blue text
And all Status badges display consistent styling and colors
```

### User Interaction

**Scenario 6: User clicks on DMR reference to view details**

```gherkin
Given a pharmacy professional is on the Manage DMRs page
And the DMR history table is displayed with at least one record
When the user clicks on a DMR reference link (e.g., "DMR002")
Then the user is navigated to the DMR Consultation Detail page
And the consultation details for that specific DMR are displayed
And the browser back button returns to the Manage DMRs page
And the user's scroll position and table state are preserved
```

**Scenario 7: User initiates a new DMR consultation**

```gherkin
Given a pharmacy professional is on the Manage DMRs page
And the patient has an active DMR registration
When the user clicks the "New DMR" button
Then the user is navigated to the New DMR initiation page
And the patient context is maintained and passed to the new page
And the patient's details (name, NHS number) are pre-populated on the next page
```

**Scenario 8: User initiates de-registration workflow**

```gherkin
Given a pharmacy professional is on the Manage DMRs page
And the patient has an active DMR registration
When the user clicks the "De-register patient from DMR service" button
Then the user is navigated to the De-register Patient Confirmation page
And the patient context is passed to the confirmation page
And the patient's current DMR registration status is displayed for confirmation
```

### Navigation

**Scenario 9: Navigation from patient profile to Manage DMRs page**

```gherkin
Given a pharmacy professional is viewing a patient's profile
And the patient has an active DMR registration
When the user navigates to the Pharmacy services tab
Then the "Manage DMRs" option is available
And clicking "Manage DMRs" navigates to this page
And the patient context is maintained throughout navigation
And all patient details (name, NHS number, age) remain visible in the header
```

### Validation

**Scenario 10: Handle API registration check failure**

```gherkin
Given a pharmacy professional navigates to the Manage DMRs page
When the API registration check fails or returns an error
Then an error notification banner is displayed with:
  - Message: "Unable to load DMR data. Please try again."
  - Icon: Warning/error icon
  - Color: Red background with white text
And a "Retry" button is provided within the banner
And the "New DMR" button is disabled until the API succeeds
And the "De-register" button is disabled
And the DMR history table is not displayed
And focus is moved to the error banner for screen reader announcement
```

**Scenario 11: Handle DMR table load failure**

```gherkin
Given the API registration check succeeds
But the DMR history table fails to load
When an error occurs during table data retrieval
Then an error message is displayed: "Unable to load DMR records. Please try again."
And a "Retry" button is provided
And the message is styled with error color (red) and warning icon
And the "New DMR" and "De-register" buttons remain enabled
And clicking "Retry" re-attempts the table data fetch
And no data is persisted
```

### Accessibility

**Scenario 12: WCAG 2.1 AA compliance and keyboard navigation**

```gherkin
Given the Manage DMRs page is rendered
When a user navigates using keyboard only
Then all interactive elements are keyboard accessible and focusable:
  - "New DMR" button
  - "De-register patient from DMR service" button
  - All DMR reference links in the table
And the tab order is logical and sequential:
  1. "New DMR" button
  2. "De-register" button
  3. First DMR reference link
  4. Subsequent DMR reference links in table order
  5. All other interactive elements
And screen readers announce:
  - Page heading: "Manage DMRs"
  - Button labels: "New DMR", "De-register patient from DMR service"
  - Table headers: "DMR reference", "Date", "Status"
  - Link context: "DMR002, consultation on 02/02/2026"
And all text has a minimum color contrast ratio of 4.5:1
And status badges display both color and text labels (not color alone)
And error messages have role="alert" and aria-live="polite"
And the page is fully functional without any mouse/pointer device
```

---

## Notes

- **Historical data ordering:** DMR records should display in reverse chronological order (most recent first) for better usability
- **Status badge colors:** Ensure status colors align with the DHCW Design System V2 badge component definitions
- **Button styling:** The "De-register" button should use red outline style (destructive action) to indicate risk; do not use filled red background
- **API integration:** The registration check should complete before displaying the page content; provide clear loading feedback
- **Migration logic:** Legacy registration migration should be transparent to the user—no separate confirmation needed; success should be implied by successful page load
- **Related stories:** Update story IDs in Related Stories section once dependent stories are created in Azure DevOps
