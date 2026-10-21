# UI User Story: Reporting Dashboard - Metrics Overview

## Story Type: UI

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator  
**I want** to view consultation and registration metrics with pharmacy and date range filtering  
**So that** I can monitor service performance trends and identify patterns across service types

### Context
Choose Pharmacy NextGen provides five consultation services (CAS, IPS, EMS, CS, DMR) and tracks patient registrations across multiple pharmacies. Service administrators need a quick-view dashboard to monitor key metrics and see how performance varies over time or across locations. This dashboard serves as the primary entry point for performance monitoring.

The metrics dashboard displays:
- **Consultation metrics** — Quick-view cards showing consultation counts by service type
- **Registration metrics** — Patient registration counts by service type
- **Pharmacy selection** — Filter data by single pharmacy or aggregate across multiple managed locations
- **Date range filtering** — Analyze trends across different time periods
- **Responsive design** — Works seamlessly on desktop, tablet, and mobile devices

### Out of Scope

- Real-time data updates or automatic refresh
- Export functionality (CSV, Excel, PDF)
- Chart/graph visualizations (cards only)
- User preference storage (filter selections not persisted on page reload)
- Pharmacy location lookup or geolocation services
- Permission/authorization logic (assumes users have access to assigned pharmacies)
- Reports library or detailed analytical reports (separate story)
- Drill-down into individual records

### Related/Dependent Stories
- [UI-Reports-Library] — Detailed reports tab (separate feature)
- [Consultation Submission API] — Provides data for consultation metrics
- [Registration API] — Provides patient registration counts
- [Authentication & Authorization] — Ensures only authorized users access dashboard

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Components used in this story:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Application header with branding
- [Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6242-6391) — Pharmacy location dropdown selection
- [Checkboxes](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Multi-pharmacy selection
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Metric display cards (consultations and registrations)
- [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6058-4625) — Date range filter options
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Section containers and descriptions
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Mock-up:** `mock-designs/dashboard-mock.html` (Dashboard tab section)

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Dashboard displays page header and metric overview**

```gherkin
Given the user navigates to the Reporting Dashboard
When the page loads
Then the following elements are displayed:
  - Header: "Reporting Dashboard" with subtitle "Overview of consultations and registrations across all services"
  - Page title: "Dashboard"
  - Pharmacy selection section with:
    - Select dropdown labeled "Your Pharmacy" (default: "Cardiff Pharmacy - Llanishen")
    - Checkboxes labeled "View data for multiple pharmacies" with options for all pharmacy locations
    - Summary panel showing "Viewing data for: Cardiff Pharmacy - Llanishen"
  - Section heading: "Consultations" with description panel
  - Date range filter options (Last week, Last month, Last 3 months, Last 12 months, All)
  - Consultation cards in a responsive grid layout displaying:
    - CAS Consultations
    - IPS Consultations
    - EMS Consultations
    - CS Consultations
    - DMR Consultations
  - Section heading: "Registrations" with description panel
  - Registration cards in a grid layout displaying:
    - CAS Registrations
    - DMR Registrations
And each metric card displays:
  - Service type label (uppercase)
  - Large numeric value (consultation/registration count)
  - Time period label (e.g., "Last 30 days")
  - Trend badge with direction indicator (↑/↓/→) and percentage
And the page header contains NHS Wales Digital branding
And the layout is responsive with grid cards stacking appropriately on mobile (below 768px)
```

### Default State

**Scenario 2: Dashboard filters default to "Last month" and current pharmacy**

```gherkin

```gherkin
Given the user navigates to the Dashboard
When the page loads for the first time
Then "Last month" date range filter is selected (radio button checked)
And "Cardiff Pharmacy - Llanishen" is the selected pharmacy in the dropdown
And the "Cardiff Pharmacy" checkbox is checked in multi-pharmacy section
And all other pharmacy checkboxes are unchecked
And consultation cards display data for the last 30 days
And each card's meta text shows "Last 30 days"
And the pharmacy summary shows: "Viewing data for: Cardiff Pharmacy - Llanishen"
And the trend percentages and direction indicators are displayed
```

### User Interaction

**Scenario 3: User changes date range filter to update metrics**

```gherkin
Given the user is on the Dashboard
And the "Last month" filter is currently selected
When the user clicks the "Last 3 months" radio button
Then the radio button becomes checked
And the visual state updates immediately
And all consultation and registration cards update with new data:
  - Card values change to reflect the new time period
  - Card meta text updates to "Last 90 days"
  - Trend percentages update
  - Trend direction indicators (↑/↓/→) and styling update (green for positive, red for negative, neutral for no change)
And the new filter selection persists while the user is on the Dashboard
When the user selects "Last week"
Then card data updates to show metrics for the last 7 days
And card meta text updates to "Last 7 days"
When the user selects "Last 12 months"
Then card data updates to show metrics for the last 12 months
When the user selects "All"
Then card data updates to show all-time metrics
```

**Scenario 4: User selects single pharmacy location from dropdown**

```gherkin
Given the user is on the Dashboard
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
And the selected pharmacy persists while the user remains on the Dashboard
```

**Scenario 5: User selects multiple pharmacies using checkboxes**

```gherkin
Given the user is on the Dashboard
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
When the user selects a different pharmacy from the dropdown
Then the multi-select checkboxes reset to only that pharmacy checked
And the pharmacy summary and dashboard metrics update accordingly
```

### Navigation

**Scenario 6: Metrics persist when user leaves and returns to dashboard**

```gherkin
Given the user is on the Dashboard with "Last 3 months" and "Swansea Pharmacy" selected
When the user navigates away from the Dashboard (to another page or tab)
And then returns to the Dashboard
Then the "Last 3 months" filter remains selected
And "Swansea Pharmacy" remains selected in the dropdown
And the dashboard metrics display for the previously selected date range and pharmacy
And no data is reset or lost
```

### Validation

**Scenario 7: Responsive design displays correctly on all breakpoints**

```gherkin
Given the page is rendered on a mobile device (width 375px)
When the user views the Dashboard
Then the pharmacy selector stacks vertically with adequate spacing
And the card grid displays in a single column
And the filter radio buttons stack vertically
And the Header and Footer remain fully visible
And all text is readable without horizontal scrolling
And touch targets for radio buttons and checkboxes are at least 44px in height
When the page is rendered on a tablet device (width 768px)
Then the card grid displays in 2-3 columns
And the filter options wrap with appropriate spacing
When the page is rendered on desktop (width 1200px+)
Then the card grid displays in up to 5 columns (responsive to container)
And the layout matches the DHCW Design System V2 responsive specifications
```

### Accessibility

**Scenario 8: WCAG 2.1 AA keyboard navigation and screen reader support**

```gherkin
Given the page is rendered
When a user navigates using only a keyboard
Then all interactive elements are reachable via Tab key:
  - Pharmacy dropdown
  - All checkboxes in multi-pharmacy section
  - All radio buttons in date range filter group
And the tab order is logical from top to bottom:
  1. Pharmacy dropdown
  2. Multi-pharmacy checkboxes (first to last)
  3. Date range filter radio buttons (first to last)
And the pharmacy dropdown has aria-label="Select your pharmacy location"
And the radio button group has role="group" with appropriate labeling
And screen reader announces:
  - "Your Pharmacy, select field" when focused on the dropdown
  - "Cardiff Pharmacy - Llanishen, currently selected option" for active pharmacy
  - "Swansea Pharmacy, checkbox, unchecked" for unselected pharmacies
  - "Last month, radio button, selected" when a filter is checked
And colour contrast ratios meet WCAG AA standard (4.5:1 for normal text, 3:1 for large text)
  - Card titles (#626a6f) against white background: 5.2:1 ✓
  - Status badges: green text (#28a745) against green background (#e5f0e3): 6.1:1 ✓
  - Status badges: red text (#d32f2f) against red background (#ffe5e5): 6.8:1 ✓
And all cards and interactive elements have visible focus indicators
And data is presented in semantic HTML with proper heading hierarchy (h2, h3 for sections)
```

**Scenario 9: Date and pharmacy filter changes announce to screen readers**

```gherkin
Given the user is on the Dashboard with screen reader enabled
When the user changes the date range filter
Then the pharmacy summary panel has role="status" with aria-live="polite"
And screen reader announces the updated summary: "[X pharmacies selected]" or pharmacy name
When the user changes pharmacy selection
Then the metric cards update
And screen reader announces the updated selection
And the announcement occurs without requiring screen reader to re-scan the entire page
```

**Scenario 10: Consultation and registration cards are clearly labeled**

```gherkin
Given the page is rendered with a screen reader
When a screen reader user navigates the card grid
Then each card is announced with:
  - Service type (e.g., "CAS Consultations")
  - Numeric value (e.g., "1,247")
  - Time period (e.g., "Last 30 days")
  - Trend information (e.g., "up 12 percent")
And cards use semantic HTML (article, heading, paragraph tags)
And aria-labels provide context for visual-only information (trend icons)
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
- [ ] Date filters update all metrics instantly
- [ ] Pharmacy selection dropdown works correctly with all locations
- [ ] Multi-pharmacy checkboxes update data and summary correctly
- [ ] At least one pharmacy is always selected (validation works)
- [ ] No console errors or warnings
- [ ] Keyboard navigation tested end-to-end
- [ ] Screen reader testing completed (NVDA/JAWS on Windows, VoiceOver on macOS)
- [ ] Performance: Dashboard loads in under 2 seconds
- [ ] Mock-up artifact (`mock-designs/dashboard-mock.html`) provided as reference

---

## Notes

### Design Reference
- Mock-up provided: `mock-designs/dashboard-mock.html` (Dashboard section) in the repository
- DHCW Design System V2 Figma: https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

### Data Considerations
- Dashboard maintains pharmacy and date filter selections while user is on this page
- Selection does not persist across page reloads (separate story for preference storage)
- Data updates are immediate (no loading states required for this story)
- Mock data provided for demonstration; backend integration separate story
- Consultation services: CAS, IPS, EMS, CS, DMR
- Date range filters: Last week (7 days), Last month (30 days), Last 3 months (90 days), Last 12 months (365 days), All
- Pharmacy locations: Cardiff, Swansea, Newport, Wrexham, Bangor (mock data)
- Aggregation logic: When multiple pharmacies selected, sum/aggregate metrics across all selected locations

### Future Enhancements (Not in scope)
- Real-time data updates with WebSocket/SignalR
- Export reports to CSV/Excel/PDF
- Persistent user preferences/bookmarks
- Data caching and pagination
- Chart/graph visualizations
- Drill-down into detailed transaction data
- Alert/notification rules based on thresholds

### Known Issues / Dependencies
- Requires authentication middleware to verify user access rights
- Backend API must support pharmacy-scoped and multi-pharmacy aggregated queries
- Date range filtering assumes server-side or comprehensive mock data
