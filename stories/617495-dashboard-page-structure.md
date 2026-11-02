# UI: Dashboard - Metrics Overview Page Structure

## Story Type: UI | Parent Story: 617495 | Effort: 5 hours

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator  
**I want** to view a dashboard with consultation and registration metrics displayed in metric cards  
**So that** I can quickly see the current state of service performance

### Context
Choose Pharmacy NextGen provides five consultation services (CAS, IPS, EMS, CS, DMR) and tracks patient registrations across multiple pharmacies. Service administrators need a dashboard that displays key metrics at a glance. This story focuses on the core dashboard page structure, layout, and metric card display. Filtering functionality (date range, pharmacy selection) is handled in a separate story.

### Out of Scope
- Date range filtering logic
- Pharmacy selection and filtering
- Real-time data updates or automatic refresh
- Export functionality (CSV, Excel, PDF)
- Chart/graph visualizations (cards only)
- User preference storage (filter selections not persisted on page reload)
- Pharmacy location lookup or geolocation services
- Permission/authorization logic (assumes users have access to assigned pharmacies)
- Reports library or detailed analytical reports (separate story)
- Drill-down into individual records
- Multi-pharmacy selection via checkboxes

### Related Stories
- UI: Dashboard - Date Range and Pharmacy Selection Filters (617495-part-2) — filters applied to this page
- UI: Dashboard - Responsive Design (617495-part-3) — cross-device optimization of this page
- UI: Dashboard - Accessibility (617495-part-4) — keyboard and screen reader support for this page

### Dependent Stories
- None (this is the foundation story)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Header](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9567) — Page header with title and subtitle
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Metric cards for each service type (7 cards total)
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Pharmacy summary section
- [Footer](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9566) — Application footer

**Mock-up:** `mock-designs/dashboard-mock.html` (Dashboard section)

---

## Acceptance Criteria

**Total Scenarios: 6**

### Page Structure & Content

**Scenario 1: Dashboard displays header, metric cards, and pharmacy summary**

```gherkin
Given a service administrator navigates to the Reporting Dashboard
When the page loads
Then the following elements are displayed:
  - Page header: "Reporting Dashboard"
  - Page subtitle: "Overview of consultations and registrations across all services"
  - Metric cards grid containing 7 cards:
    * Consultation services: CAS, IPS, EMS, CS, DMR
    * Registration types: CAS Registrations, DMR Registrations
  - Pharmacy summary panel showing current pharmacy selection
  - Application footer
And each metric card displays:
  - Service name/type (e.g., "CAS Consultations")
  - Metric value (large numeric value)
  - Metric label (e.g., "Consultations completed")
  - Time period label (e.g., "Last 30 days")
  - Trend indicator (↑/↓/→ with color: green for up, red for down, grey for stable)
And the cards are arranged in a responsive grid layout
And each card has consistent padding and spacing
```

**Scenario 2: Default page state shows Last month data for current pharmacy**

```gherkin
Given the page loads for the first time
When the user has not yet applied any filters
Then the metric cards display data for:
  - Time period: Last 30 days ("Last month")
  - Pharmacy: Current user's assigned pharmacy (e.g., "Cardiff Pharmacy - Llanishen")
And the pharmacy summary panel displays:
  - Text: "Showing data for: [Pharmacy Name]"
  - Selected pharmacy icon
And each metric card shows:
  - A representative value (mock data or real baseline)
  - Correct trend indicator based on data
  - The 30-day time period label
And the layout is clean with no visual clutter
```

### Default State

**Scenario 3: Metric card values and trends are properly formatted**

```gherkin
Given the metric cards are rendered with data
When the page displays the cards
Then each card's metric value is formatted as:
  - Numbers >= 1,000 use thousand separators (e.g., "1,234" not "1234")
  - Numbers < 1,000 display without separators (e.g., "42")
And each card's trend indicator displays:
  - Green up arrow (↑) for positive trend (increase > 0%)
  - Red down arrow (↓) for negative trend (decrease > 0%)
  - Grey dash (→) for stable trend (change = 0% or minimal)
And each card's time period label displays the filter period:
  - "Last 7 days" for weekly data
  - "Last 30 days" for monthly data
  - "Last 90 days" for quarterly data
  - "Last 365 days" for annual data
  - "All time" for all-time data
And the pharmacy summary shows:
  - "Showing data for: [Single Pharmacy Name]" when one pharmacy is selected
  - "Showing data for: [Count] pharmacies" when multiple are selected
```

### User Interaction

**Scenario 4: Metric cards are visually distinct and tappable areas**

```gherkin
Given the metric cards are displayed on the dashboard
When the user hovers over a card (desktop) or focuses on a card (mobile)
Then the card displays a subtle hover/focus state:
  - Box shadow increases or background color shifts slightly
  - Cursor changes to pointer on desktop
  - Card remains in focus on mobile/tablet
And the card dimensions are at least 120px × 120px to ensure tappable area
And no action is triggered when the user interacts with the card in this story
  (card tap/click actions are handled in future stories if drill-down is implemented)
And the interaction does not navigate the user away from the dashboard
```

### Navigation

**Scenario 5: Page persists in session and returns to dashboard**

```gherkin
Given the user is viewing the dashboard
When the user navigates away from the dashboard (to another page)
And then returns to the dashboard page
Then the page reloads with:
  - The same metric cards displayed
  - The same data values shown (same time period, same pharmacy)
  - The header and footer in their original positions
And the user's filter selections are retained
  (filter state persistence is verified in part-2 story)
And no data loss occurs
```

### Validation

**Scenario 6: Page layout and card grid structure are correct**

```gherkin
Given the dashboard page has loaded
When the page renders the metric cards
Then the grid structure meets these criteria:
  - Cards are arranged in rows and columns
  - Consultation cards (CAS, IPS, EMS, CS, DMR) are grouped logically
  - Registration cards (CAS Registrations, DMR Registrations) are grouped logically
  - Cards have equal height within a row
  - Card padding is consistent (16px or design-system standard)
  - Gaps between cards are uniform (8px or design-system standard)
And the grid is left-aligned with appropriate margins:
  - Desktop: 24px left/right margins
  - Tablet: 16px left/right margins
  - Mobile: 12px left/right margins
And no cards overflow or wrap unexpectedly
And the page has no horizontal scroll on any viewport
And the pharmacy summary panel aligns with the card grid
And the footer is positioned at the bottom of the viewport or content, whichever is lower
```

### Accessibility

**Scenario 7: WCAG 2.1 AA semantic structure and labeling**

```gherkin
Given the dashboard page is rendered
When a screen reader user accesses the page
Then the page structure meets WCAG 2.1 AA requirements:
  - Page has a single h1 tag: "Reporting Dashboard"
  - Page subtitle is semantic text (not h2, not a heading)
  - Each metric card is marked as an article element with proper role="article"
  - Each card's service name is marked as h3 (e.g., <h3>CAS Consultations</h3>)
  - Each card's value is wrapped in a span with aria-label for context:
    e.g., <span aria-label="1,234 consultations completed">1,234</span>
  - Metric label is a paragraph <p> with descriptive text
  - Trend indicator has aria-label:
    e.g., <span aria-label="trend up">↑</span>
  - Pharmacy summary panel has role="region" aria-label="Pharmacy Summary"
And the semantic structure allows screen readers to navigate:
  - By headings (h1, h3)
  - By regions (role="region")
  - By landmarks (main, footer)
And color contrast ratios meet 4.5:1 minimum:
  - Text on metric card background: 4.5:1+
  - Trend indicators (green, red, grey) have sufficient contrast with background
  - Header and footer text have 4.5:1+ contrast
And all text is readable at 16px font size or larger
```

---

## Definition of Done

- [ ] Dashboard page created with header, metric cards, and footer
- [ ] 7 metric cards implemented (5 consultation services + 2 registration types)
- [ ] Metric card layout uses responsive grid (refinement in part-3 story)
- [ ] Default data shows Last month (30 days) for current pharmacy
- [ ] Metric values formatted with thousand separators
- [ ] Trend indicators (↑/↓/→) display with color coding (green/red/grey)
- [ ] Time period label displays on each card
- [ ] Pharmacy summary panel implemented
- [ ] Page header and footer in place
- [ ] Card hover/focus states implemented
- [ ] All acceptance criteria passing (7 scenarios)
- [ ] Unit tests for data formatting and card rendering
- [ ] WCAG 2.1 AA semantic HTML and accessibility checks
- [ ] Component variants match DHCW Design System V2
- [ ] Code review passed
- [ ] Ready for part-2 filter implementation

---

## Technical Notes

### Card Data Structure
Each metric card should display:
- `serviceName`: string (e.g., "CAS Consultations")
- `value`: number (e.g., 1234)
- `label`: string (e.g., "Consultations completed")
- `trend`: "up" | "down" | "stable"
- `period`: string (e.g., "Last 30 days")

### Grid Layout (refined in part-3 for responsiveness)
- Desktop: 5 cards per row (consultation) + 2 cards per row (registration)
- Card minimum width: 200px
- Card aspect ratio: approximately 1:1 or 4:3

### Pharmacy Summary Data
- `pharmacyName`: string
- `pharmacyCode`: string
- `selectedCount`: number (for multi-pharmacy in part-2)

---

## Related Standards and Documentation

- [COMPONENTS.md](../COMPONENTS.md) — Design system components
- [UI-Template.md](../templates/UI-Template.md) — UI story template guidelines
- **DHCW Design System V2:** [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938)
