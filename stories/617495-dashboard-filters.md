# UI: Dashboard - Date Range and Pharmacy Selection Filters

## Story Type: UI | Parent Story: 617495 | Effort: 8 hours

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator  
**I want** to filter dashboard metrics by date range and pharmacy selection  
**So that** I can view performance data for specific time periods and specific pharmacies

### Context
The dashboard displays key metrics, but administrators need flexibility to view data for different time periods (last 7 days, 30 days, 90 days, all-time) and across multiple pharmacies. This story implements the filter controls and the logic to update metrics when filters change. The page structure and layout come from part-1 story; this story adds interactivity and data binding.

### Out of Scope
- Custom date range picker (fixed preset periods only: 7, 30, 90, 365 days)
- Export/download functionality
- Filter history or saved filter presets
- Real-time data updates or WebSocket subscriptions
- Batch updates to multiple pharmacies simultaneously
- Drill-down analytics or detailed filtering by service type
- Advanced filters (by consultant, by outcome type, etc.)
- API pagination or lazy loading of data
- Cache invalidation or refresh timing logic

### Related Stories
- UI: Dashboard - Metrics Overview Page Structure (617495-part-1) — provides the baseline page structure
- UI: Dashboard - Responsive Design (617495-part-3) — adapts filter controls to mobile/tablet
- UI: Dashboard - Accessibility (617495-part-4) — keyboard and screen reader support for filters

### Dependent Stories
- None (part-1 does not depend on this, but this story depends on part-1)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Filter period selection buttons (7d, 30d, 90d, All-time)
- [Dropdown/Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Pharmacy selection dropdown
- [Checkboxes](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Multi-pharmacy selection (optional toggle)
- [Inset Text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4589) — Filter status message

**Mock-up:** `mock-designs/dashboard-filters.html` (Filters section)

---

## Acceptance Criteria

**Total Scenarios: 8**

### Page Structure & Content

**Scenario 1: Filter controls are displayed above metric cards**

```gherkin
Given the user is on the Reporting Dashboard
When the page loads
Then the filter controls are displayed in this order:
  - Filter panel heading: "Filter Data"
  - Date range filter group with label: "Date Range"
    * Four filter buttons: "7 Days", "30 Days", "90 Days", "All-time"
  - Pharmacy filter group with label: "Pharmacy"
    * Dropdown/select list showing current selected pharmacy
    * Option to expand dropdown for selection
  - Filter status text: "Showing data for: [Pharmacy Name], Last [period]"
And the filter controls are positioned above the metric cards
And the filter panel has a light background to visually distinguish it from cards
And all filter elements are properly labeled and grouped
```

**Scenario 2: Default filter state on page load**

```gherkin
Given the dashboard loads for the first time
When the page initializes
Then the default filter state is set to:
  - Date range: "30 Days" (button highlighted/active)
  - Pharmacy: Currently logged-in user's assigned pharmacy
  - Filter status text displays: "Showing data for: [Current Pharmacy Name], Last 30 days"
And the "30 Days" button has visual indication of being selected:
  - Darker background color or border highlighting
And the pharmacy dropdown displays:
  - The pharmacy name (e.g., "Cardiff Pharmacy - Llanishen")
  - A down arrow icon indicating it can be expanded
And all metric cards show data for the default filters
```

### Default State

**Scenario 3: Date range buttons display with correct styling**

```gherkin
Given the filter panel is displayed
When the user views the date range buttons
Then each button displays:
  - Button text: "7 Days", "30 Days", "90 Days", or "All-time"
  - Consistent button size and padding
  - Normal state: Light background, dark text
  - Active state (selected): Darker/colored background, white text
  - Hover state: Slightly darker shade, cursor pointer
And only ONE button is highlighted as active at a time
And the buttons are arranged horizontally in a row
And buttons have adequate spacing between them (8px minimum)
```

**Scenario 4: Pharmacy dropdown displays available options**

```gherkin
Given the pharmacy filter dropdown is displayed
When the user clicks/taps the dropdown to expand it
Then a list of available pharmacies is displayed:
  - All pharmacies the current user has access to
  - Each pharmacy entry shows: Name and ODS code
  - e.g., "Cardiff Pharmacy - Llanishen (W21000")
  - Each pharmacy is clickable/tappable
And the currently selected pharmacy is marked/highlighted in the list
And the dropdown list is positioned below the select field
And the list has a scrollbar if more than 5 pharmacies are available
And the dropdown can be closed by:
  - Clicking on a pharmacy name (selects it)
  - Clicking elsewhere on the page
  - Pressing Escape key (handled in part-4 accessibility)
```

### User Interaction

**Scenario 5: Selecting a date range updates metric cards**

```gherkin
Given the user is viewing the dashboard with default filters (30 Days)
When the user clicks on the "7 Days" button
Then the following updates occur immediately:
  - The "7 Days" button becomes highlighted (active state)
  - The "30 Days" button is no longer highlighted
  - All metric cards are updated to show data for the last 7 days
  - Each card's time period label changes to: "Last 7 days"
  - Metric values update to reflect 7-day data
  - Trend indicators are recalculated based on 7-day data
  - Filter status text updates to: "Showing data for: [Pharmacy], Last 7 days"
And no page reload occurs (smooth, client-side update)
And the user remains on the same page
And other filters (pharmacy selection) are not affected
```

**Scenario 6: Selecting a pharmacy updates metric cards and status text**

```gherkin
Given the user is viewing the dashboard with default pharmacy selected
When the user clicks on the pharmacy dropdown
And selects a different pharmacy from the list (e.g., "Swansea Pharmacy")
Then the following updates occur immediately:
  - The pharmacy dropdown now displays: "Swansea Pharmacy - ODS-code"
  - All metric cards update to show data for the selected pharmacy
  - Metric values are recalculated for the new pharmacy
  - Trend indicators are recalculated for the new pharmacy
  - Filter status text updates to: "Showing data for: Swansea Pharmacy, Last [period]"
  - The dropdown closes automatically
And no page reload occurs
And the date range filter remains unchanged
And the currently active date range button remains highlighted
```

**Scenario 7: Filter combinations update metrics correctly**

```gherkin
Given the user has the dashboard open with default filters
When the user:
  - Changes the date range to "90 Days"
  - Then changes the pharmacy to "Bangor Pharmacy"
Then the metric cards display:
  - Data for Bangor Pharmacy
  - Data for the last 90 days
  - Correct metric values
  - Trend indicators based on 90-day data
  - Correct time period label: "Last 90 days"
  - Filter status text: "Showing data for: Bangor Pharmacy, Last 90 days"
And each filter change immediately updates the cards
And filter changes do not affect each other (independent updates)
```

### Navigation

**Scenario 8: Filter state persists during session**

```gherkin
Given the user has applied filters (e.g., "7 Days", "Caerphilly Pharmacy")
When the user navigates away from the dashboard
And returns to the dashboard page
Then the filter state is restored:
  - The previously selected date range button is highlighted
  - The previously selected pharmacy is displayed in the dropdown
  - The metric cards show data for the restored filters
  - The filter status text shows the restored selections
And no manual reapplication of filters is required
And the filter state persists for the duration of the user's session
And closing the browser or logging out clears the filter state
```

### Validation

**Scenario 9: Metric card data updates are accurate**

```gherkin
Given the user has applied a filter
When the metric cards are updated
Then the displayed data is accurate:
  - Metric values match the queried data for the selected filters
  - No stale data is displayed
  - Trend calculations are correct (comparing two consecutive periods)
  - Time period labels match the selected filter
And the data load completes within 2 seconds of filter change
And if data load fails:
  - An error message displays: "Unable to load metrics. Please try again."
  - The error is displayed near the filter panel
  - error has role="alert" for screen reader announcement
  - The metric cards retain their previous values (no blank state)
  - The user can retry by selecting a different filter or re-applying the same filter
And no data is persisted during failed updates
```

### Accessibility

**Scenario 10: WCAG 2.1 AA filter controls with keyboard navigation**

```gherkin
Given the filter panel is displayed
When a keyboard user navigates the dashboard
Then the filter controls meet WCAG 2.1 AA requirements:
  - All filter buttons are keyboard focusable (Tab key)
  - All filter buttons can be activated with Enter or Space key
  - The pharmacy dropdown is keyboard focusable
  - The dropdown can be opened/closed with Enter, Space, or Arrow keys
  - Tab order is logical: "7 Days" → "30 Days" → "90 Days" → "All-time" → "Pharmacy Dropdown"
  - Current focus is visually indicated with:
    * Focus outline (2px+ border or outline)
    * Focus color contrast 3:1+ against background
  - Aria-pressed="true" attribute on active date range button
  - aria-expanded="true/false" on pharmacy dropdown
  - aria-label on buttons: "Select 7 day period" (or similar descriptive label)
  - Pharmacy dropdown has aria-label: "Select pharmacy"
And when a filter is changed via keyboard:
  - Screen reader announces: "Date range changed to 7 days" (or similar)
  - aria-live="polite" region announces filter updates
  - Focus remains on the button that was activated
And Escape key closes the pharmacy dropdown (if open)
And all text in filter controls has 4.5:1+ color contrast
```

---

## Definition of Done

- [ ] Filter panel implemented above metric cards
- [ ] Date range filter buttons created (7, 30, 90, All-time)
- [ ] Pharmacy selection dropdown implemented
- [ ] Default filter state set correctly (30 Days, current user's pharmacy)
- [ ] Filter status text displays current selections
- [ ] Date range selection updates all metric cards
- [ ] Pharmacy selection updates all metric cards
- [ ] Filter state persists during user session
- [ ] Error handling for failed metric updates
- [ ] All acceptance criteria passing (10 scenarios)
- [ ] Unit tests for filter state management
- [ ] Integration tests for metric updates on filter change
- [ ] WCAG 2.1 AA keyboard navigation and screen reader support
- [ ] Focus management and visual indicators
- [ ] Performance verified (metric updates within 2 seconds)
- [ ] Code review passed
- [ ] Ready for part-3 responsive design refinement

---

## Technical Notes

### Date Range Configuration
- **7 Days:** `startDate = today() - 7 days`, `endDate = today()`
- **30 Days:** `startDate = today() - 30 days`, `endDate = today()`
- **90 Days:** `startDate = today() - 90 days`, `endDate = today()`
- **All-time:** `startDate = account creation date or null`, `endDate = today()`

### Pharmacy Selection
- Load available pharmacies from user profile or pharmacy service
- Filter by user's role/permissions
- Display format: `"[Pharmacy Name] - [ODS Code]"`
- Store selected pharmacy in component state or session storage

### Metric Update Flow
1. User changes filter (button click or dropdown selection)
2. Update filter state (Redux, Vuex, or component state)
3. Trigger API call with new filter parameters
4. Update metric cards with new data
5. Announce change to screen readers (aria-live)

### API Contract (Expected)
```
GET /api/v1/dashboards/metrics
  ?pharmacyId={id}
  &startDate={YYYY-MM-DD}
  &endDate={YYYY-MM-DD}

Response:
{
  "metrics": [
    {
      "serviceType": "CAS",
      "value": 1234,
      "trend": "up",
      "periodLabel": "Last 30 days"
    },
    ...
  ],
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

## Related Standards and Documentation

- [COMPONENTS.md](../COMPONENTS.md) — Design system components (Buttons, Dropdowns, Checkboxes)
- [UI-Template.md](../templates/UI-Template.md) — UI story template guidelines
- **DHCW Design System V2:**
  - [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475)
  - [Dropdown/Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008)
