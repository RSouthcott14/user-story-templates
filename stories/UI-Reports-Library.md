# UI User Story: Reports Library

## Story Type: UI

---

## Description

### Story Card
**As a** Choose Pharmacy service analyst  
**I want** to access a library of pre-built analytical reports with pharmacy and date range filtering  
**So that** I can investigate detailed performance metrics and generate insights for specific time periods and pharmacy locations

### Context
Service analysts need access to detailed analytical reports beyond the quick-view metrics dashboard. The Reports Library provides access to 11 pre-built reports covering consultation patterns, staff performance, medications, and data quality. Analysts must be able to filter reports by date range and pharmacy location, similar to the dashboard, but with independent filter state.

The reports library displays:
- **Report directory** — Table listing 11 available analytical reports
- **Report metadata** — Description, status (Available/Draft), and action buttons for each report
- **Pharmacy filtering** — Select single pharmacy or aggregate across multiple locations
- **Date range filtering** — Analyze reports across different time periods
- **Status indicators** — Shows which reports are ready (Available) vs. in development (Draft)

### Out of Scope
- Actual report data generation or viewing
- Report export functionality (CSV, Excel, PDF)
- Custom report builder or ad-hoc query tool
- Report scheduling or automation
- Report sharing or permissions
- Report history or versioning
- Chart/graph rendering
- Permission/authorization logic for specific reports (assumes all reports accessible)

### Related/Dependent Stories
- [UI-Dashboard-Metrics] — Dashboard metrics overview tab (separate feature)
- [Reports Generation Service] — Backend API for report data and metadata
- [Authentication & Authorization] — Ensures only authorized users access reports

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Components used in this story:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with branding
- [Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391) — Pharmacy location dropdown selection
- [Checkboxes](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Multi-pharmacy selection
- [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625) — Date range filter options
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Section containers and descriptions
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Reports list display
- [Tag](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-78) — Status badges (Available/Draft)
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Mock-up:** `mock-designs/dashboard-mock.html` (Reports tab section)

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Reports tab displays page header and report library**

```gherkin
Given the user navigates to the Reports tab
When the tab loads
Then the following elements are displayed:
  - Section heading: "Available Reports"
  - Pharmacy selection section with:
    - Select dropdown labeled "Your Pharmacy" (default: "Cardiff Pharmacy - Llanishen")
    - Checkboxes labeled "View data for multiple pharmacies" with options for all pharmacy locations
    - Summary panel showing "Viewing data for: Cardiff Pharmacy - Llanishen"
  - Date range filter options (Last week, Last month, Last 3 months, Last 12 months, All)
  - Panel with title "Report Library" and description text
  - Table listing all 11 available reports with columns:
    - Report Name (linked text)
    - Description
    - Status badge (Available or Draft)
    - Action ("View" button)
And the table displays the following reports:
  - Consultation per Staff Member
  - Consultations per Staff Group
  - Consultations per Condition
  - Medications provided per Condition
  - Consultations per Pharmacy (Multiples)
  - Consultation Outcomes
  - Medications provided outside of Spec (IPS)
  - Consultation Duration
  - Removed/Deleted Records
  - High number of consultations
  - Incomplete Consultations
And the layout is responsive with horizontal scroll on mobile (below 768px)
```

### Default State

**Scenario 2: Reports tab filters default to "Last month" and current pharmacy**

```gherkin
Given the user navigates to the Reports tab
When the tab loads for the first time
Then "Last month" date range filter is selected (radio button checked)
And "Cardiff Pharmacy - Llanishen" is the selected pharmacy in the dropdown
And the "Cardiff Pharmacy" checkbox is checked in multi-pharmacy section
And all other pharmacy checkboxes are unchecked
And the reports table is displayed with data for that date range and pharmacy
And the pharmacy summary shows: "Viewing data for: Cardiff Pharmacy - Llanishen"
```

**Scenario 3: Draft reports are disabled**

```gherkin
Given the Reports tab is displayed
When the user views the reports table
Then reports with status "Available" (green badge) have enabled "View" buttons
And the report "Medications provided outside of Spec (IPS)" displays status "Draft" (yellow badge)
And the "View" button for Draft reports is disabled (grayed out)
And hovering over or focusing a disabled button provides no action
And a user cannot click through to draft reports
```

### User Interaction

**Scenario 4: User changes date range to update report availability**

```gherkin
Given the user is on the Reports tab
And the "Last month" filter is currently selected
When the user clicks the "Last 3 months" radio button
Then the radio button becomes checked
And the visual state updates immediately
And the reports table updates to reflect data for the last 90 days
And the report descriptions and metadata remain consistent
And the new filter selection persists while the user is on the Reports tab
When the user selects "Last week"
Then the reports table updates for the last 7 days
When the user selects "Last 12 months"
Then the reports table updates for the last 12 months
When the user selects "All"
Then the reports table updates to show all-time data
```

**Scenario 5: User selects single pharmacy from dropdown**

```gherkin
Given the user is on the Reports tab
And "Cardiff Pharmacy - Llanishen" is the currently selected pharmacy
When the user clicks the "Your Pharmacy" dropdown
Then the dropdown expands showing all available pharmacy locations
When the user selects "Newport Pharmacy - High Street"
Then the dropdown closes
And the select field updates to show "Newport Pharmacy - High Street"
And the multi-pharmacy checkboxes reset:
  - Only "Newport Pharmacy" is checked
  - All other checkboxes are unchecked
And the pharmacy summary updates to show: "Viewing data for: Newport Pharmacy - High Street"
And the reports table updates to display data for Newport Pharmacy
And the selected pharmacy persists while the user remains on the Reports tab
When the user navigates back to the Dashboard tab
Then the Dashboard tab retains its own independent pharmacy selection
```

**Scenario 6: User selects multiple pharmacies for report aggregation**

```gherkin
Given the user is on the Reports tab
And "Cardiff Pharmacy - Llanishen" is the currently selected pharmacy
And only the Cardiff checkbox is checked
When the user clicks the "Swansea Pharmacy" checkbox
Then the checkbox becomes checked
And the pharmacy summary updates to show: "Viewing data for: 2 pharmacies selected"
And the reports table updates to display aggregated data for both pharmacies
When the user clicks the "Wrexham Pharmacy" checkbox
Then the checkbox becomes checked
And the pharmacy summary updates to show: "Viewing data for: 3 pharmacies selected"
And the reports table updates to display aggregated data for all three pharmacies
When the user unchecks the "Swansea Pharmacy" checkbox
Then the checkbox becomes unchecked
And the pharmacy summary updates to show: "Viewing data for: 2 pharmacies selected"
And the reports table updates to display data for only Cardiff and Wrexham
When the user attempts to uncheck all checkboxes
Then the system prevents unchecking all boxes:
  - At least one pharmacy must remain selected at all times
  - The last remaining checkbox cannot be unchecked
When the user selects a different pharmacy from the dropdown
Then the multi-select checkboxes reset to only that pharmacy checked
And the reports table data updates accordingly
```

### Navigation

**Scenario 7: Reports filters maintain independent state from Dashboard tab**

```gherkin
Given the user is on the Dashboard tab with "Last 3 months" and "Swansea Pharmacy" selected
When the user clicks the Reports tab
Then the Reports tab loads with its own default filters:
  - "Last month" selected
  - "Cardiff Pharmacy - Llanishen" selected
And the Reports tab does not inherit or reference the Dashboard tab filters
When the user changes the Reports date range to "Last 12 months" and pharmacy to "Newport Pharmacy"
And then switches back to the Dashboard tab
Then the Dashboard tab still shows "Last 3 months" and "Swansea Pharmacy"
And switching back to Reports again shows "Last 12 months" and "Newport Pharmacy"
And filter selections are maintained independently per tab
```

### Validation

**Scenario 8: Responsive design displays correctly on all breakpoints**

```gherkin
Given the page is rendered on a mobile device (width 375px)
When the user views the Reports tab
Then the pharmacy selector stacks vertically with adequate spacing
And the filter radio buttons stack vertically
And the reports table displays with horizontal scroll capability
And columns remain readable with adequate width
And all text is readable without vertical scrolling of the entire page
And touch targets for radio buttons and checkboxes are at least 44px in height
When the page is rendered on a tablet device (width 768px)
Then the filter options wrap with appropriate spacing
And the table displays with improved spacing between columns
When the page is rendered on desktop (width 1200px+)
Then the filter options display inline in a single row
And the table displays with full width and all columns visible
```

### Accessibility

**Scenario 9: WCAG 2.1 AA keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using only a keyboard
Then all interactive elements are reachable via Tab key:
  - Pharmacy dropdown
  - All checkboxes in multi-pharmacy section
  - All radio buttons in date range filter group
  - All "View" buttons in table rows
And the tab order is logical:
  1. Pharmacy dropdown
  2. Multi-pharmacy checkboxes (first to last)
  3. Date range filter radio buttons (first to last)
  4. View buttons in table rows (top to bottom)
And when a "View" button is focused, it displays a visible focus indicator
And disabled "View" buttons are skipped in tab order (or remain focusable but non-actionable)
And the pharmacy dropdown has aria-label="Select your pharmacy location for reports"
And the radio button group has role="group" with appropriate labeling
And screen reader announces:
  - "Your Pharmacy, select field" when focused on the dropdown
  - "Newport Pharmacy, checkbox, unchecked" for unselected pharmacies
  - "Last month, radio button, selected" when a filter is checked
  - "Consultation per Staff Member, button, View" for enabled report buttons
  - "Medications provided outside of Spec, button, disabled" for draft report buttons
And colour contrast ratios meet WCAG AA standard (4.5:1 for normal text, 3:1 for large text)
  - Status badges: green text (#28a745) against green background (#e5f0e3): 6.1:1 ✓
  - Status badges: yellow text (#856404) against yellow background (#fff3cd): 6.8:1 ✓
  - Button text on background meets 4.5:1 minimum
And all buttons and interactive elements have visible focus indicators
```

**Scenario 10: Data table has proper semantic structure for screen readers**

```gherkin
Given the Reports table is rendered
When a screen reader user navigates the table
Then the table has proper semantic structure:
  - thead with th elements (scope="col") for column headers
  - tbody with tr and td elements for data rows
  - Table has role="table" or semantic table element
  - Column headers are announced (Report Name, Description, Status, Action)
  - Row data is announced with context (report name followed by description, status, button)
And the table structure allows screen readers to navigate by column or row
And each report name is announced as a link or prominent element
And status badges are announced clearly (e.g., "Available" or "Draft")
And action buttons are announced with their purpose (e.g., "View, button")
```

---

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches DHCW Design System V2 (Figma/mock-up provided)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested (320px, 375px, 768px, 1024px, 1200px breakpoints)
- [ ] Date filters update report metadata correctly
- [ ] Pharmacy selection dropdown works correctly
- [ ] Multi-pharmacy checkboxes aggregate report data correctly
- [ ] At least one pharmacy is always selected (validation works)
- [ ] Draft reports display with disabled View buttons
- [ ] Reports tab filters remain independent from Dashboard tab filters
- [ ] No console errors or warnings
- [ ] Keyboard navigation tested end-to-end
- [ ] Screen reader testing completed (NVDA/JAWS on Windows, VoiceOver on macOS)
- [ ] Table semantic structure verified (thead, tbody, th, tr, td)
- [ ] Performance: Reports tab loads in under 2 seconds
- [ ] Mock-up artifact (`mock-designs/dashboard-mock.html`) provided as reference

---

## Notes

### Design Reference
- Mock-up provided: `mock-designs/dashboard-mock.html` (Reports tab section) in the repository
- DHCW Design System V2 Figma: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

### Reports Included

| Report Name | Status | Notes |
|---|---|---|
| Consultation per Staff Member | Available | Performance by individual staff member |
| Consultations per Staff Group | Available | Aggregated by role/group |
| Consultations per Condition | Available | Segmented by diagnosis |
| Medications provided per Condition | Available | Medication supply metrics |
| Consultations per Pharmacy (Multiples) | Available | Multi-site pharmacy performance |
| Consultation Outcomes | Available | Results and outcomes summary |
| Medications provided outside of Spec (IPS) | Draft | IPS out-of-spec dispensing |
| Consultation Duration | Available | Length and time-to-completion |
| Removed/Deleted Records | Available | Audit trail of removed records |
| High number of consultations | Available | Unusually high volume identification |
| Incomplete Consultations | Available | Unfinished submissions |

### Data Considerations
- Reports tab maintains independent pharmacy and date filter selections
- Filter selections do not persist across page reloads (separate story for preference storage)
- Data updates are immediate (no loading states required for this story)
- Mock data provided for demonstration; backend API integration separate story
- Date range filters: Last week (7 days), Last month (30 days), Last 3 months (90 days), Last 12 months (365 days), All
- Pharmacy locations: Cardiff, Swansea, Newport, Wrexham, Bangor (mock data)
- Report status: Available (production-ready) or Draft (in development)
- Aggregation logic: When multiple pharmacies selected, aggregate/union report data across all selected locations

### Future Enhancements (Not in scope)
- Actual report viewing/detailed data display
- Export to CSV/Excel/PDF
- Custom report builder
- Report scheduling and automation
- Report sharing with other users
- Report history and versioning
- Real-time data updates
- Chart/graph visualizations
- Report caching and offline access

### Known Issues / Dependencies
- Requires authentication middleware to verify user access rights
- Backend API must provide report metadata (name, description, status)
- Backend API must support pharmacy-scoped and multi-pharmacy report queries
- Report status (Available/Draft) determined by backend configuration
- Actual report data retrieval is a separate story/implementation
