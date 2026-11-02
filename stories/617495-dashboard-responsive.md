# UI: Dashboard - Responsive Design

## Story Type: UI | Parent Story: 617495 | Effort: 6 hours

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator using mobile or tablet devices  
**I want** the dashboard to display correctly across all screen sizes  
**So that** I can access metrics from any device (phone, tablet, desktop)

### Context
Service administrators access the Choose Pharmacy dashboard from various devices: mobile phones, tablets, and desktops. The dashboard structure and filters are defined in part-1 and part-2 stories. This story focuses on making the layout responsive, ensuring content is readable and interactive controls are usable on all viewport sizes. Breakpoints follow DHCW Design System V2 standards.

### Out of Scope
- Offline functionality or service worker caching
- Progressive Web App (PWA) features
- Orientation change persistence (landscape/portrait)
- Tablet-specific gesture controls (swipe, pinch)
- Mobile-specific navigation drawer (main app nav is out of scope)
- Print stylesheet or print-to-PDF functionality
- Landscape-only or portrait-only enforcement
- Image optimization or lazy loading
- Font scaling or zoom controls

### Related Stories
- UI: Dashboard - Metrics Overview Page Structure (617495-part-1) — baseline layout
- UI: Dashboard - Date Range and Pharmacy Selection Filters (617495-part-2) — filter control responsiveness
- UI: Dashboard - Accessibility (617495-part-4) — keyboard and screen reader on all viewport sizes

### Dependent Stories
- None (this story builds on part-1 and part-2)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Responsive Breakpoints (DHCW Design System V2):**
- **Mobile:** 320px – 767px
- **Tablet:** 768px – 1024px
- **Desktop:** 1025px+

**Components used (all breakpoints):**
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Metric cards
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Filter buttons (reflow on mobile)
- [Dropdown/Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — Pharmacy selector

**Mock-up:** `mock-designs/dashboard-responsive.html` (Mobile, Tablet, Desktop views)

---

## Acceptance Criteria

**Total Scenarios: 7**

### Page Structure & Content

**Scenario 1: Mobile layout (320px – 767px) displays stacked card grid**

```gherkin
Given the user accesses the dashboard on a mobile device (screen width 320px–767px)
When the page loads
Then the layout adapts with:
  - Page header (h1 "Reporting Dashboard") remains centered
  - Page subtitle is visible below header
  - Filter controls stack vertically:
    * "Date Range" label above buttons
    * Buttons wrap into 2 rows of 2 buttons each (7d / 30d, 90d / All-time)
    * Each button expands to fill available width on its row
    * Pharmacy dropdown below filter buttons, full width
  - Metric cards display in a single column (1 card per row)
  - Each card has full viewport width minus margins (16px left/right)
  - Card height is approximately 120px (consistent with desktop)
  - Pharmacy summary panel is full width below cards
  - Footer remains at bottom
And no horizontal scrolling occurs
And content is readable without zooming
And touch targets are at least 44px × 44px
```

**Scenario 2: Tablet layout (768px – 1024px) displays 2-column grid**

```gherkin
Given the user accesses the dashboard on a tablet device (screen width 768px–1024px)
When the page loads
Then the layout adapts with:
  - Page header and subtitle centered
  - Filter controls display horizontally:
    * "Date Range" label on left
    * Buttons in a row: "7 Days", "30 Days", "90 Days", "All-time"
    * Each button sized to fit the row without wrapping
    * Pharmacy filter on the same row to the right of date buttons
      (or flows to a second row if space is tight)
  - Metric cards display in a 2-column grid:
    * Consultation services (5 cards) in rows of 2 cards
    * Registration types (2 cards) fill the bottom row (2 cards wide)
    * Card width is approximately (viewport width - 32px) / 2
  - Card height is consistent with desktop (approximately 120px)
  - Pharmacy summary panel below cards, full width
  - Footer remains at bottom
And cards do not overlap or clip
And filter buttons maintain readable text size
And no horizontal scrolling occurs
```

**Scenario 3: Desktop layout (1025px+) displays full-width card grid**

```gherkin
Given the user accesses the dashboard on a desktop device (screen width 1025px+)
When the page loads
Then the layout displays as designed:
  - Page header and subtitle centered
  - Filter controls horizontal:
    * Date range buttons in a row
    * Pharmacy dropdown to the right
    * All controls fit on a single line
  - Metric cards display in a 5-column grid:
    * Consultation services (CAS, IPS, EMS, CS, DMR) in first row
    * Registration types (CAS Registrations, DMR Registrations) in second row
    * Each card width is (viewport width - 48px) / 5
  - Card height is consistent (approximately 120px)
  - Pharmacy summary panel below cards, full width
  - Footer remains at bottom
  - Maximum content width is 1400px (or design-system standard)
  - Adequate left/right margins on desktop (24px)
And all content is visible without scrolling (unless many metrics are added)
And cards maintain consistent spacing
```

### Default State

**Scenario 4: Text remains readable at all breakpoints**

```gherkin
Given the dashboard is displayed at any breakpoint
When text is rendered
Then all text meets readability requirements:
  - Page header (h1) is at least 24px on mobile, 28px+ on desktop
  - Filter labels are at least 14px on all breakpoints
  - Button text is at least 14px on all breakpoints
  - Metric values are at least 18px on all breakpoints (scaled for prominence)
  - Metric labels are at least 12px on all breakpoints
  - Pharmacy summary text is at least 14px on all breakpoints
  - Line height is at least 1.5 for paragraph text (readability standard)
  - No text is truncated or clipped on mobile
And font scaling is relative (using rem or em units)
And text remains legible without zooming
```

**Scenario 5: Card proportions and spacing remain consistent**

```gherkin
Given metric cards are displayed at any breakpoint
When the cards are rendered
Then the card dimensions meet these criteria:
  - Mobile: Each card is full-width minus margins (16px left/right)
  - Tablet: Each card is approximately (viewport width - 32px) / 2
  - Desktop: Each card is approximately (viewport width - 48px) / 5
  - Card height is consistent across all breakpoints (approximately 120px)
  - Aspect ratio is maintained for visual consistency
  - Card padding is adjusted for screen size:
    * Mobile: 12px padding
    * Tablet: 14px padding
    * Desktop: 16px padding
  - Gaps between cards:
    * Mobile: 8px
    * Tablet: 10px
    * Desktop: 12px
And cards do not distort or become disproportionate
And content inside cards remains aligned and readable
```

### User Interaction

**Scenario 6: Touch targets and interactive elements are adequate on mobile**

```gherkin
Given the user is on a mobile device (touch screen)
When the user attempts to interact with filter buttons or pharmacy dropdown
Then all interactive elements have adequate touch targets:
  - Minimum size: 44px × 44px (WAG-WCAG standard for mobile)
  - Minimum size: 48px × 48px (recommended for comfort)
  - Buttons have sufficient spacing to avoid accidental selection (8px minimum gap)
  - Pharmacy dropdown is easy to tap and open
  - Dropdown list items are at least 44px tall for easy selection
And there is no need to zoom in to interact with controls
And hover states are replaced with focus/active states on mobile
And tap feedback is immediate (no lag or delay)
```

**Scenario 7: Layout remains stable during orientation change**

```gherkin
Given the user is viewing the dashboard on a mobile or tablet device
When the user rotates the device from portrait to landscape (or vice versa)
Then the layout reflows correctly:
  - Viewport width updates to match new orientation
  - Metric cards reflow to the new column configuration
  - Filter controls adjust to the new width
  - All content remains visible without horizontal scrolling
  - No content is cut off or hidden
And the reflow completes smoothly without jumps or flashing
And the user can interact with all elements in both orientations
And filter selections are retained after orientation change
```

### Navigation

**Scenario 8: Navigation and scrolling work smoothly on all viewports**

```gherkin
Given the user is navigating the dashboard on any device
When the user scrolls vertically through the page
Then the scrolling behavior is smooth:
  - Page header remains visible (sticky or always at top)
  - Filter controls remain accessible (sticky on desktop, scroll on mobile)
  - Metric cards scroll smoothly below filters
  - Pharmacy summary and footer are accessible at bottom
And horizontal scrolling does not occur (except for unavoidable OS scrollbars)
And back-to-top navigation is available on mobile (if content is long)
And no layout shift occurs during scroll
```

### Validation

**Scenario 9: Responsive design is tested across browsers and devices**

```gherkin
Given the dashboard is deployed
When tested on multiple devices and browsers
Then the layout displays correctly:
  - Chrome/Edge on mobile (Android, latest version)
  - Safari on mobile (iOS, latest version)
  - Chrome/Edge on tablet (Android)
  - Safari on tablet (iPadOS)
  - Chrome on desktop (Windows, macOS, Linux)
  - Edge on desktop
  - Firefox on desktop
And the layout is pixel-perfect at each breakpoint (within 2px tolerance)
And no console errors occur related to responsive styles
And CSS media queries are properly applied
And viewport meta tag is correctly set: <meta name="viewport" content="width=device-width, initial-scale=1">
And images and icons scale correctly at all breakpoints
```

### Accessibility

**Scenario 10: Responsive layout maintains WCAG 2.1 AA accessibility**

```gherkin
Given the dashboard is displayed at any breakpoint
When accessibility checks are performed
Then the layout meets WCAG 2.1 AA requirements:
  - Contrast ratios remain 4.5:1+ on all breakpoints
  - Focus indicators are visible and adequate on mobile (not hidden by layout)
  - Text resizing: page remains usable at 200% zoom on desktop, 150% on mobile
  - Reflow: at 320px width, content single-columns without loss of information
  - No functionality is hidden based on screen size alone
  - Color is not used as the only means of conveying information:
    * Trend indicators use icons (↑/↓/→) plus color
    * Active filter button uses background color plus text/border distinction
  - Links and buttons have sufficient spacing and are not ambiguous
  - Screen reader announces text correctly at all breakpoints
And semantic HTML remains valid at all breakpoints
And aria-labels and aria-live regions function correctly on mobile
```

---

## Definition of Done

- [ ] Mobile breakpoint (320px–767px) implemented and tested
- [ ] Tablet breakpoint (768px–1024px) implemented and tested
- [ ] Desktop breakpoint (1025px+) implemented and tested
- [ ] CSS media queries used for responsive behavior
- [ ] Viewport meta tag is correctly configured
- [ ] Card grid uses CSS Grid or Flexbox for responsive layout
- [ ] Filter controls reflow correctly at each breakpoint
- [ ] Text remains readable at all breakpoints (font sizing, line height)
- [ ] Touch targets are minimum 44px × 44px on mobile
- [ ] No horizontal scrolling occurs at any breakpoint
- [ ] Orientation change (portrait/landscape) handled smoothly
- [ ] All acceptance criteria passing (10 scenarios)
- [ ] Cross-browser testing completed (Chrome, Firefox, Edge, Safari)
- [ ] Mobile device testing completed (iOS, Android)
- [ ] Tablet device testing completed
- [ ] WCAG 2.1 AA accessibility maintained at all breakpoints
- [ ] Performance verified (layout reflow < 1 second)
- [ ] Code review passed
- [ ] Ready for part-4 accessibility refinement

---

## Technical Notes

### CSS Media Queries
```css
/* Mobile first approach */
@media (min-width: 768px) {
  /* Tablet styles */
}

@media (min-width: 1025px) {
  /* Desktop styles */
}
```

### Metric Card Grid
- **Mobile:** `display: grid; grid-template-columns: 1fr;` (1 column)
- **Tablet:** `grid-template-columns: repeat(2, 1fr);` (2 columns)
- **Desktop:** `grid-template-columns: repeat(5, 1fr);` (5 columns for services, adjust for registrations)

### Filter Button Reflow
- **Mobile:** Buttons wrap into rows of 2 (use Flexbox with flex-wrap)
- **Tablet/Desktop:** Buttons stay in single row (flex-wrap: nowrap)

### Viewport Meta Tag
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0">
```

### Touch Target Size
- Minimum 44px × 44px (WCAG standard)
- Recommended 48px × 48px for comfort
- Minimum 8px spacing between targets

---

## Related Standards and Documentation

- [COMPONENTS.md](../COMPONENTS.md) — Design system components
- [UI-Template.md](../templates/UI-Template.md) — UI story template guidelines
- **DHCW Design System V2:** [Responsive Design Guidelines](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)
- **Mobile Testing:** [BrowserStack](https://www.browserstack.com/) or [Responsively App](https://responsively.app/)
