# UI User Story: Reporting Dashboard

## Story Type: UI

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator  
**I want** to view consultation and registration metrics with filterable reports  
**So that** I can monitor service performance, track staff activity, and generate data-driven insights across all pharmacy consultation services

### Context
Choose Pharmacy NextGen provides five consultation services (CAS, IPS, EMS, CS, DMR) and tracks patient registrations across multiple pharmacies. Service administrators and performance analysts need a centralized reporting dashboard to monitor key metrics, identify trends, and generate detailed reports. This dashboard serves as the primary entry point for performance monitoring and operational decision-making.

Superintendants and managers may oversee single or multiple pharmacy locations and need the ability to:
- View data for their current primary location (default)
- Switch between different pharmacy locations
- Aggregate data across multiple pharmacies they manage

The reporting dashboard displays:
- **Pharmacy selection** — Filter data by single pharmacy or aggregate across multiple
- **Overview metrics** — Quick-view consultation counts and registrations by service
- **Flexible date filtering** — Analyze trends across different time periods
- **Available reports** — Access to detailed analytical reports for deeper investigation
- **Multi-tab interface** — Organized into Dashboard (overview) and Reports (detailed) tabs

### Out of Scope
- Real-time data updates or automatic refresh
- Export functionality (CSV, Excel, PDF) for reports
- Custom report builder or report scheduling
- Data drill-down into individual patient or staff member records
- Alert/notification rules based on thresholds
- Chart/graph visualizations (cards only, no charts)
- User preference storage (filter selections not persisted on page reload)
- Pharmacy location lookup or geolocation services
- Permission/authorization logic (assumes users have access to assigned pharmacies)

### Related/Dependent Stories
- [Consultation Submission API] — Provides data for consultation metrics
- [Registration API] — Provides patient registration counts
- [Authentication & Authorization] — Ensures only authorized users access dashboard
- [Reports Generation Service] — Powers detailed report data

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Components used in this story:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with branding
- [Tabs](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6165-80) — Dashboard and Reports tab navigation
- [Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391) — Pharmacy location dropdown selection
- [Checkboxes](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Multi-pharmacy selection
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Metric display cards (consultations and registrations)
- [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625) — Date range filter options
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Section containers and descriptions
- [Table](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6164-79) — Reports list display
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 12**

### Page Structure & Content

**Scenario 1: Dashboard tab displays page header, pharmacy selection, and consultation overview**

```gherkin
Given the user navigates to the Reporting Dashboard
When the page loads
Then the following elements are displayed:
  - Header: "Reporting Dashboard" with subtitle "Overview of consultations and registrations across all services"
  - Tab navigation: "Dashboard" (active) and "Reports" tabs
  - Pharmacy selection section with:
    - Select dropdown labeled "Your Pharmacy" (default: "Cardiff Pharmacy - Llanishen")
    - Checkboxes labeled "View data for multiple pharmacies" with options for all pharmacy locations
    - Summary panel showing "Viewing data for: Cardiff Pharmacy - Llanishen"
  - Section heading: "Consultations"
  - Date range filters (Last week, Last month, Last 3 months, Last 12 months, All)
  - Consultation cards in a grid layout displaying:
    - CAS Consultations
    - IPS Consultations
    - EMS Consultations
    - CS Consultations
    - DMR Consultations
  - Section heading: "Registrations"
  - Registration cards in a grid layout displaying:
    - CAS Registrations
    - DMR Registrations
And each card displays:
  - Service type label (uppercase)
  - Large numeric value (consultation/registration count)
  - Time period label (e.g., "Last 30 days")
  - Trend badge with direction indicator (↑/↓/→) and percentage
And the page header contains NHS Wales Digital branding
And the layout is responsive with grid cards stacking on mobile (below 768px)
And the pharmacy select dropdown is focused by default (or first interactive element in tab order)
```

**Scenario 2: Reports tab displays available reports library**

```gherkin
Given the user is on the Reporting Dashboard
When the user clicks the "Reports" tab
Then the Reports tab becomes active
And the following elements are displayed:
  - Section heading: "Available Reports"
  - Date range filters (same as Dashboard tab)
  - Panel with title "Report Library" and description text
  - Table listing all 11 available reports:
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
And each table row displays:
  - Report Name (as clickable link text)
  - Description of report content
  - Status badge ("Available" in green or "Draft" in yellow)
  - "View" button (enabled for Available, disabled for Draft reports)
And the table is fully responsive with horizontal scroll on mobile
```

### Default State

**Scenario 3: Dashboard filters default to "Last month" selection**

```gherkin
Given the user navigates to the Reporting Dashboard
When the page loads for the first time
Then the "Last month" date range filter is selected (radio button checked)
And consultation cards display data for the last 30 days
And each card's meta text shows "Last 30 days"
And the trend percentages and directions are displayed
And the filter radio buttons are all visible with proper styling
```

**Scenario 4: Reports tab filters also default to "Last month"**

```gherkin
Given the user navigates to the Reports tab
When the tab loads for the first time
Then the "Last month" date range filter is selected (radio button checked)
And the Reports table is displayed with data based on the selected date range
And the filter state is independent from the Dashboard tab filters
```

### User Interaction

**Scenario 5: User switches between Dashboard and Reports tabs**

```gherkin
Given the user is on the Reporting Dashboard
When the user clicks the "Reports" tab
Then the Reports tab becomes active with visual indicator (blue underline)
And the "Dashboard" tab becomes inactive
And the Dashboard content is hidden
And the Reports content is displayed
And focus moves to the Reports tab button
When the user clicks the "Dashboard" tab
Then the Dashboard tab becomes active
And the Reports tab becomes inactive
And the Dashboard content is displayed with all previous filter selections retained
And the Reports content is hidden
```

**Scenario 6: User changes date range filter on Dashboard tab**

```gherkin
Given the user is on the Dashboard tab
And the "Last month" filter is currently selected
When the user clicks the "Last 3 months" radio button
Then the radio button becomes checked
And the visual state updates immediately
And all consultation cards update with new data:
  - Card values change to reflect the new time period
  - Card meta text updates to "Last 90 days"
  - Trend percentages update
  - Trend direction indicators (↑/↓/→) and styling update accordingly (green for positive, red for negative, neutral for no change)
And the filter selection persists while the user is on the tab
When the user selects "Last week"
Then card data updates to show metrics for the last 7 days
And card meta text updates to "Last 7 days"
When the user selects "Last 12 months"
Then card data updates to show metrics for the last 12 months
When the user selects "All"
Then card data updates to show all-time metrics
```

**Scenario 7: User changes date range filter on Reports tab**

```gherkin
Given the user is on the Reports tab
And the "Last month" filter is currently selected
When the user clicks a different date range radio button (e.g., "Last 3 months")
Then the radio button becomes checked
And the Reports table data updates based on the selected date range
And the filter selection is independent from Dashboard tab filters
And the user can navigate back to Dashboard tab without affecting the Dashboard's filter state
```

### Navigation

**Scenario 8: Tab navigation maintains state and data integrity**

```gherkin
Given the user is on the Dashboard tab with "Last 3 months" filter selected
When the user clicks the "Reports" tab
And then immediately clicks back to the "Dashboard" tab
Then the "Last 3 months" filter remains selected
And the Dashboard data displays for the last 90 days (the previously selected range)
And the card values match the expected data for that time period
And no data is lost or reset
```

### Validation

**Scenario 9: Responsive design displays correctly on mobile and tablet**

```gherkin
Given the page is rendered on a mobile device (width 375px)
When the user views the Dashboard tab
Then the card grid displays in a single column (responsive to screen width)
And the filter radio buttons stack vertically with adequate spacing
And the Header and Footer remain fully visible
And all text is readable without horizontal scrolling
And touch targets for radio buttons are at least 44px in height
When the page is rendered on a tablet device (width 768px)
Then the card grid displays in 2-3 columns
And the filter options wrap with appropriate spacing
When the page is rendered on desktop (width 1200px+)
Then the card grid displays in 5 columns (or responsive to container)
And the layout matches the DHCW Design System V2 responsive specifications
```

### Accessibility

**Scenario 10: WCAG 2.1 AA keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using only a keyboard
Then all interactive elements are reachable via Tab key:
  - Dashboard tab button
  - Reports tab button
  - All radio buttons in filter group
  - All "View" buttons in Reports table (visible when tab is active)
And the tab order is logical from top to bottom:
  1. Dashboard tab button
  2. Reports tab button
  3. Date range filter radio buttons (first to last)
  4. View buttons in table rows (if on Reports tab)
And each tab button has aria-selected="true" when active and aria-selected="false" when inactive
And the radio button group has role="group" with aria-labelledby pointing to the filter label
And the tabs have role="tab" and role="tablist" attributes
And screen reader announces:
  - "Dashboard tab, selected" when focused on active Dashboard tab
  - "Reports tab" when focused on inactive Reports tab
  - "Filter options group" when entering the date range filter group
  - "Last month, radio button, selected" when a filter is checked
And colour contrast ratios meet WCAG AA standard (4.5:1 for normal text, 3:1 for large text)
  - Active tab border (#005bba) against white background: 8.5:1 ✓
  - Card titles (#626a6f) against white background: 5.2:1 ✓
  - Status badges: green text (#28a745) against green background (#e5f0e3): 6.1:1 ✓
  - Status badges: yellow text (#856404) against yellow background (#fff3cd): 6.8:1 ✓
And all cards and interactive elements have visible focus indicators (focus ring)
And there is a skip link to jump directly to main content (if header/nav present)
And data table has proper semantic structure:
  - thead with th elements (scope="col")
  - tbody with tr and td elements
  - Table has role="table" or semantic table element
```

### Pharmacy Selection

**Scenario 11: User selects single pharmacy location from dropdown**

```gherkin
Given the user is on the Dashboard tab
And "Cardiff Pharmacy - Llanishen" is the currently selected pharmacy
When the user clicks the "Your Pharmacy" dropdown
Then the dropdown expands showing all available pharmacy locations:
  - Cardiff Pharmacy - Llanishen (currently selected)
  - Swansea Pharmacy - City Centre
  - Newport Pharmacy - High Street
  - Wrexham Pharmacy - Town Centre
  - Bangor Pharmacy - High Street
When the user selects "Swansea Pharmacy - City Centre"
Then the dropdown closes
And the select field updates to show "Swansea Pharmacy - City Centre"
And the multi-pharmacy checkboxes reset:
  - Only "Swansea Pharmacy" is checked
  - All other checkboxes are unchecked
And the pharmacy summary updates to show: "Viewing data for: Swansea Pharmacy - City Centre"
And all consultation and registration cards update to display data for Swansea Pharmacy
And the selected pharmacy persists while the user is on the Dashboard tab
When the user switches to the Reports tab
Then the Reports tab has its own independent pharmacy selection (defaults to original pharmacy)
When the user switches back to the Dashboard tab
Then the previously selected pharmacy (Swansea) remains selected
```

**Scenario 12: User selects multiple pharmacies using checkboxes**

```gherkin
Given the user is on the Dashboard tab
And "Cardiff Pharmacy - Llanishen" is the currently selected pharmacy
And only the Cardiff checkbox is checked
When the user clicks the "Swansea Pharmacy" checkbox
Then the checkbox becomes checked
And the pharmacy summary updates to show: "Viewing data for: 2 pharmacies selected"
And all consultation and registration cards update to display aggregated data for both pharmacies
When the user clicks the "Newport Pharmacy" checkbox
Then the checkbox becomes checked
And the pharmacy summary updates to show: "Viewing data for: 3 pharmacies selected"
And all consultation and registration cards update to display aggregated data for all three pharmacies
When the user unchecks the "Newport Pharmacy" checkbox
Then the checkbox becomes unchecked
And the pharmacy summary updates to show: "Viewing data for: 2 pharmacies selected"
And all consultation and registration cards update to display data for only Cardiff and Swansea
When the user attempts to uncheck all checkboxes (including the currently checked ones)
Then the system prevents unchecking all boxes:
  - At least one pharmacy must remain selected at all times
  - The last remaining checkbox cannot be unchecked
  - If the user tries to uncheck the last box, it remains checked with no data change
And when the user selects a different pharmacy from the dropdown
Then the multi-select checkboxes reset to only that pharmacy checked
And the pharmacy summary and dashboard data update accordingly
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
- [ ] Tab switching functionality works smoothly with no data loss
- [ ] Date filters update all relevant cards/tables instantly
- [ ] Pharmacy selection dropdown works correctly with all locations
- [ ] Multi-pharmacy checkboxes update data and summary correctly
- [ ] At least one pharmacy is always selected (validation works)
- [ ] Dashboard and Reports tabs maintain independent pharmacy selections
- [ ] Pharmacy selection persists when switching between tabs and back
- [ ] No console errors or warnings
- [ ] Keyboard navigation tested end-to-end (including pharmacy selectors)
- [ ] Screen reader testing completed (NVDA/JAWS on Windows, VoiceOver on macOS)
- [ ] Performance: Page loads in under 2 seconds
- [ ] Mock-up artifact (dashboard-mock.html) provided as reference

---

## Notes

### Design Reference
- Mock-up provided: `dashboard-mock.html` in the repository
- DHCW Design System V2 Figma: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

### Data Considerations
- Dashboard and Reports tabs maintain independent filter states (including pharmacy selection)
- Each tab has its own pharmacy selector that operates independently
- Users can select single or multiple pharmacies
- At least one pharmacy must always be selected (business rule)
- Data updates are immediate (no loading states required for this story)
- Mock data provided for demonstration; backend integration separate story
- Consultation services: CAS, IPS, EMS, CS, DMR
- Date range filters: Last week (7 days), Last month (30 days), Last 3 months (90 days), Last 12 months (365 days), All
- Pharmacy locations: Cardiff, Swansea, Newport, Wrexham, Bangor (mock data)
- Aggregation logic: When multiple pharmacies selected, sum/aggregate metrics across all selected locations

### Future Enhancements (Not in scope)
- Real-time data updates with WebSocket/SignalR
- Export reports to CSV/Excel/PDF
- Custom report builder
- Report scheduling and automation
- Drill-down into detailed transaction data
- Chart/graph visualizations
- Data caching and pagination for large datasets

### Known Issues / Dependencies
- Requires authentication middleware to verify user access rights
- Reports table requires backend API for report metadata
- Date range filtering assumes server-side or comprehensive mock data
