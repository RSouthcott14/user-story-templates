# UI User Story: Patient Service Registration Management

## Story Type: UI | Feature: 617640 | US: TBD

---

## Description

### Story Card
**As a** Choose Pharmacy pharmacy user  
**I want** to view and manage a patient's Common Ailments Service (CAS) and Discharge Medicines Review (DMR) service registrations  
**So that** I can ensure patients are appropriately registered for pharmacy services and that registration status remains accurate and up to date

### Context
Within the Patient Management Hub, pharmacy users need visibility of a patient's service registration status and the ability to register or deregister patients from supported services. This story covers the UI for managing CAS and DMR registrations with toggle-based controls, registration history, and confirmation flows.

**Related Journey Step**: Patient Management Hub → Service Registration Management → Registration History

### Out of Scope
- Eligibility checking for service registration
- Bulk registration updates
- Automated registration from external systems
- Registration to services beyond CAS and DMR (future enablement in separate stories)
- Custom registration fields or metadata
- Reports or analytics on registration data

### Related/Dependent Stories
- TBD (API backend for registration management)
- TBD (Permissions and authorization rules)
- TBD (Registration audit trail backend)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Toggle controls for registration status
- [Expander](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-142) — Collapsible registration details and history
- [Summary List](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-77) — Display registration details (dates, pharmacy, registered by)
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Registration history timeline
- [Notification Banners](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6610-4602) — Confirmation/success/error messages
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Service registration section container
- [Important Information](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=7785-6796) — Deregistration confirmation dialog

**Error messaging standard:** See [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 9**

### Page Structure & Content

**Scenario 1: Service Registration section displays available services with current status**

```gherkin
Given the user navigates to the Patient Management Hub for a patient
When the page loads
Then the following Service Registrations section is displayed:
  - Section heading: "Service Registrations"
  - Service cards or list items for each available service (CAS and DMR)
  - Each service displays:
    - Service name (e.g., "Common Ailments Service" / "Discharge Medicines Review")
    - Current registration status (toggle switch showing On/Off state)
    - Registered status label: "Registered" or "Not Registered"
  - Visual indicator or badge showing current state (✅ On / ⬜ Off)
  - "View Details" or expand button to see full registration information
  - Inline note (if permissions restrict): "You do not have permission to manage registrations"
And the section is positioned within the patient management interface
And the layout is responsive and accessible
```

**Scenario 2: Current Registration Details display when service is registered**

```gherkin
Given a service (CAS or DMR) shows as "Registered"
When the user expands the service details or clicks "View Details"
Then the following information is displayed in a Summary List:
  - Service Name: "[Service Name]"
  - Registration Status: "Registered"
  - Registration Date: "[Date in format DD/MM/YYYY]"
  - Registration Time: "[Time in format HH:MM]"
  - Registered By: "[User Name]"
  - Current Pharmacy: "[Pharmacy Name]"
  - Previous Status: "Not Registered" (if applicable)
And the information is clearly formatted and easy to read
And a "View History" link is visible to access full audit trail
And the expander closes/collapses when clicked again
```

**Scenario 3: Service toggle for unregistered service is enabled**

```gherkin
Given the user is viewing Service Registrations
And a service (CAS or DMR) is currently "Not Registered"
When the page loads
Then the toggle control for that service displays:
  - Toggle switch in OFF position (⬜ Off)
  - Status label: "Not Registered"
  - Service summary (if applicable): "[Service description or last registration date if deregistered]"
And the toggle is enabled (user can interact with it)
And no registration details are shown (service is not registered)
And an "expand" arrow or indicator suggests additional content is available
```

### Default State

**Scenario 4: Page loads with all available services and current registration states**

```gherkin
Given the user navigates to the Patient Management Hub for a patient record
When the page loads for the first time
Then:
  - Service Registrations section is displayed
  - All enabled services (CAS and DMR) are listed
  - Each service shows its current registration status (On/Off)
  - Toggle controls are in their current state (not reset)
  - If user has permissions: toggle controls are enabled
  - If user lacks permissions: toggle controls are disabled and note is displayed
  - Registration details (if registered) are collapsed by default
  - No success or error messages are displayed
  - Focus is not automatically set to any element (ready for user interaction)
```

### User Interaction

**Scenario 5: User registers a patient to a service by enabling toggle**

```gherkin
Given the user is viewing Service Registrations
And a service (e.g., CAS) is currently "Not Registered"
And the user has permission to manage registrations
When the user clicks/toggles the toggle switch to ON
Then:
  - The toggle immediately moves to ON position (✅ On)
  - Status label changes to "Registered"
  - A confirmation notification displays: "Patient has been successfully registered for [Service Name]"
  - Notification type: success (green banner with checkmark)
  - Registration details expand automatically showing:
    - Service Name: "[Service Name]"
    - Registration Status: "Registered"
    - Registration Date: "[Current date in DD/MM/YYYY]"
    - Registration Time: "[Current time in HH:MM]"
    - Registered By: "[Current user name]"
    - Current Pharmacy: "[Patient's pharmacy from context]"
  - The toggle is now in the enabled (ON) state and user can deregister
  - The change persists when user navigates away and returns
And no data loss occurs if page is refreshed
```

**Scenario 6: User deregisters a patient from a service (with confirmation)**

```gherkin
Given the user is viewing Service Registrations
And a service (e.g., CAS) is currently "Registered"
And the user has permission to manage registrations
When the user clicks/toggles the toggle switch to OFF
Then:
  - A deregistration confirmation dialog appears with:
    - Title: "Confirm Deregistration"
    - Message: "You are about to deregister this patient from the [Service Name] service.\n\nThe patient will no longer be available for consultations under this service.\n\nDo you wish to continue?"
    - Two buttons: "Confirm" (Primary) and "Cancel" (Secondary)
  - The toggle remains in ON position until confirmed or cancelled
  - Focus moves to the "Confirm" button (or "Cancel" if appropriate)
  - Pressing Escape key closes the dialog without deregistering
And the dialog is accessible via keyboard and screen reader
```

**Scenario 7: User confirms deregistration and service is updated**

```gherkin
Given the deregistration confirmation dialog is displayed
When the user clicks "Confirm" button
Then:
  - The dialog closes
  - The toggle moves to OFF position (⬜ Off)
  - Status label changes to "Not Registered"
  - A success notification displays: "Patient has been successfully deregistered from [Service Name]"
  - Notification type: success (green banner with checkmark)
  - Registration details collapse and are no longer displayed
  - A brief summary line may show: "[Service Name] - Last registered [Date/Time ago]" (if tracking history)
  - The change persists when user navigates away and returns
And audit trail records the deregistration action
```

**Scenario 8: User cancels deregistration confirmation**

```gherkin
Given the deregistration confirmation dialog is displayed
When the user clicks "Cancel" button (or presses Escape)
Then:
  - The dialog closes immediately
  - The toggle remains in ON position (✅ On)
  - No deregistration occurs
  - Status remains: "Registered"
  - No notification is displayed
  - The service details remain visible (expanded state preserved)
  - No data is persisted or changed
```

### Navigation

**Scenario 9: User navigates to full registration history from Service Registrations**

```gherkin
Given the user is viewing Service Registrations for a patient
When the user clicks "View History" link or "Registration History" button
Then:
  - User is navigated to the Registration History page/section
  - The history page displays a table with all registration changes for the patient:
    - Columns: Service, Action, Date/Time, Performed By, Pharmacy, Previous Status, New Status
    - Rows sorted by date (most recent first)
    - Data includes both CAS and DMR registration changes
    - Example row: "CAS | Registered | 16/10/2026 14:32 | Jane Smith (j.smith@pharmacy.nhs.uk) | Pharmacy Name | Not Registered | Registered"
  - Pagination is provided if history exceeds 20 entries
  - User can return to Service Registrations via Back button
  - The selected patient context is maintained
```

### Validation

**Scenario 10: User with insufficient permissions cannot manage registrations**

```gherkin
Given the user is viewing Service Registrations
And the user does NOT have permission to manage patient registrations
When the page loads
Then:
  - The Service Registrations section is displayed
  - All toggle controls are disabled (greyed out / not interactive)
  - A note appears near the toggles: "You do not have permission to manage service registrations"
  - All registration details are visible (read-only)
  - No action can be taken to change registration status
  - User receives no error message (permission restriction is informational)
And the interface clearly indicates read-only access
```

**Scenario 11: Registration update fails due to system error**

```gherkin
Given the user attempts to register or deregister a patient from a service
When the backend API returns an error response
Then:
  - The toggle returns to its previous state (registration not persisted)
  - An error notification displays: "Registration update failed. Please try again or contact support."
  - Error type: error (red banner with error icon)
  - The error message includes details if available (e.g., "[Service] registration failed: [API error message]")
  - Focus returns to the failed toggle control
  - A "Retry" button may be provided to attempt the action again
  - No partial data is persisted
```

### Accessibility

**Scenario 12: WCAG 2.1 AA compliance for keyboard navigation and screen readers**

```gherkin
Given the Service Registrations section is rendered
When a user navigates using Tab key
Then tab order is logical:
  - Heading "Service Registrations" (not focusable)
  - Service 1 toggle switch (e.g., CAS) with associated text
  - Service 1 expand/details button (if applicable)
  - Service 2 toggle switch (e.g., DMR) with associated text
  - Service 2 expand/details button (if applicable)
  - "View History" link
  - Any other interactive elements follow in logical sequence
And each toggle switch announces via screen reader:
  - "[Service Name], toggle switch, [on/off], [registered/not registered]"
  - Toggle state changes are announced immediately: "CAS toggle switched on"
And the confirm/cancel buttons in deregistration dialog announce as:
  - "[Button Name], button"
  - Focus order in dialog: Confirm button → Cancel button
And all text has colour contrast ratio of at least 4.5:1
And the service status text is not reliant on colour alone (includes text labels and icons)
And all interactive elements have visible focus indicator (minimum 3:1 contrast)
And any icons (✅, ⬜) have associated text labels or aria-label
And expandable sections announce when opened/closed: "[Service Name] details, expanded/collapsed"
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Accessibility testing completed (WCAG 2.1 AA, tested with screen reader and keyboard)
- [ ] UI matches DHCW Design System V2 (Figma mock-up provided)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (375px, 768px, 1024px, 1440px)
- [ ] Integration with permissions/authorization layer verified
- [ ] API integration tested (registration/deregistration endpoints)
- [ ] Audit trail events logged correctly
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes
- **Feature Context**: This story implements the Service Registrations section of the Patient Management Hub (Feature 617640)
- **Backend Dependencies**: Registration management API endpoints, patient context/pharmacy context, permissions layer
- **Future Enhancement**: Story to add support for additional services (future pharmacy services) through configuration
- **Historical Data**: Registration history is audited and can be viewed through the history page/section
- **Permissions Model**: Access control is managed through user role/permission system (separate configuration)
