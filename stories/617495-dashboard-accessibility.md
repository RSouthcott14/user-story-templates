# UI: Dashboard - WCAG 2.1 AA Accessibility

## Story Type: UI | Parent Story: 617495 | Effort: 7 hours

---

## Description

### Story Card
**As a** Choose Pharmacy service administrator with disabilities or assistive technology users  
**I want** the dashboard to be fully accessible via keyboard navigation and screen readers  
**So that** I can independently access and interact with all metrics and filter controls

### Context
The Choose Pharmacy dashboard must comply with WCAG 2.1 AA accessibility standards for all users. Previous stories focused on page structure, filters, and responsive design; this story ensures full accessibility including keyboard navigation, screen reader support, semantic HTML, focus management, and color contrast. This is a comprehensive accessibility audit and implementation story for the entire dashboard.

### Out of Scope
- Speech-to-text or voice control implementation (beyond keyboard equivalents)
- WCAG AAA (Level 3) compliance (scope is AA only)
- Third-party accessibility audit or certification
- Accessibility training or documentation for users
- Sign language or video captions (not applicable to dashboard UI)
- Custom error messages beyond standardized formats
- Braille display support (screen reader responsibility)

### Related Stories
- UI: Dashboard - Metrics Overview Page Structure (617495-part-1) — baseline HTML structure
- UI: Dashboard - Date Range and Pharmacy Selection Filters (617495-part-2) — filter keyboard support
- UI: Dashboard - Responsive Design (617495-part-3) — accessibility at all breakpoints

### Dependent Stories
- None (this story is final in the dashboard suite)

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Accessibility Standards:**
- WCAG 2.1 Level AA (W3C Web Content Accessibility Guidelines)
- ARIA Authoring Practices Guide (APG) patterns
- Semantic HTML5 elements

**Components used (all with accessibility features):**
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — With proper ARIA roles
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — With keyboard support
- [Dropdown/Select](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4008) — With ARIA expanded/selected states

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Page has proper semantic HTML structure and landmarks**

```gherkin
Given the dashboard HTML is rendered
When the source code is inspected
Then the semantic structure meets WCAG 2.1 AA requirements:
  - Page has exactly one <h1> tag: "Reporting Dashboard"
  - Main content is wrapped in <main role="main"> element
  - Filter section is wrapped in <section aria-label="Dashboard Filters">
  - Each metric card is wrapped in <article role="article">
  - Pharmacy summary is wrapped in <section role="region" aria-label="Pharmacy Summary">
  - Footer is wrapped in <footer role="contentinfo">
  - Page has proper <header> with branding and user info
And all landmark regions have distinct aria-labels for screen reader navigation
And only one <main> landmark exists on the page
And no deprecated elements are used (e.g., <center>, <font>)
```

**Scenario 2: Heading hierarchy is correct and logical**

```gherkin
Given the dashboard page loads
When a screen reader user navigates by headings
Then the heading hierarchy is correct:
  - <h1>: "Reporting Dashboard" (page title)
  - <h2>: "Dashboard Filters" (filter section)
  - <h3>: Each filter group label (e.g., "Date Range", "Pharmacy")
  - <h3>: Each metric card title (e.g., "CAS Consultations", "DMR Registrations")
  - <h2>: "Pharmacy Summary" (summary section)
And no heading levels are skipped (e.g., h1 → h3 is not allowed)
And each heading describes its section accurately
And screen reader users can navigate efficiently by headings
And the structure is consistent across page reloads
```

### Default State

**Scenario 3: Color contrast ratios meet WCAG 2.1 AA standards**

```gherkin
Given the dashboard is displayed with default styling
When color contrast is measured
Then all text and UI elements meet 4.5:1 minimum contrast:
  - Normal text (body, labels, descriptions): 4.5:1+
  - Large text (18px+ or 14px+ bold): 3:1+ (relaxed standard)
  - UI components (buttons, form inputs, borders): 3:1+
  - Focus indicators: 3:1+ against background
And specific contrast ratios are verified for:
  - Page header text on background
  - Metric card titles on card background
  - Filter button text (normal and active states)
  - Filter button focus indicators
  - Pharmacy dropdown text
  - Trend indicators (↑/↓/→):
    * Green up arrow: 4.5:1+ against card background
    * Red down arrow: 4.5:1+ against card background
    * Grey dash: 4.5:1+ against card background
  - Link text (if any)
  - Error message text (red or colored)
And no text is conveyed by color alone (icons, symbols used in addition)
And contrast ratios are verified using WCAG Contrast Checker or similar tool
```

**Scenario 4: Images and icons have appropriate alt text and labels**

```gherkin
Given the dashboard displays icons and visual elements
When the page source is inspected
Then all images and icons have proper descriptions:
  - Trend indicators have aria-label:
    * "↑ trend up" for up arrow (aria-label="Trend up")
    * "↓ trend down" for down arrow
    * "→ trend stable" for dash
  - Pharmacy icon (if present) has aria-label: "Pharmacy location"
  - Any decorative icons have aria-label="" (empty) or role="presentation"
  - Service logos have aria-label describing the service
And screen readers announce the labels when focused
And icons are not used as the only means of conveying information
  (text labels or aria-labels accompany all icons)
And SVG icons have <title> tags for context
```

### User Interaction

**Scenario 5: All interactive elements are keyboard accessible**

```gherkin
Given the dashboard is displayed
When a keyboard-only user navigates the page
Then all interactive elements are reachable via keyboard:
  - Tab key moves focus to next interactive element
  - Shift+Tab moves focus to previous interactive element
  - Enter key activates buttons and opens dropdowns
  - Space key activates buttons
  - Arrow keys navigate within dropdowns:
    * Up Arrow: Move to previous item in dropdown
    * Down Arrow: Move to next item in dropdown
    * Home: Move to first item in dropdown
    * End: Move to last item in dropdown
  - Escape key closes dropdowns
And no interactive elements are unreachable (tabindex not negative)
And no keyboard traps exist (user is not stuck in one element)
And keyboard navigation is in logical, predictable order:
  - Focus starts at page header
  - Moves through filter section: Date range buttons → Pharmacy dropdown
  - Moves through metric cards (left-to-right, top-to-bottom)
  - Ends at footer
And keyboard users can complete all tasks (filter metrics, interact with controls)
```

**Scenario 6: Focus indicators are visible and clear**

```gherkin
Given the user navigates using keyboard
When focus moves to an interactive element
Then the focus indicator is clear and visible:
  - Focus outline is at least 2px thick
  - Focus color has 3:1+ contrast with background
  - Focus indicator is not hidden or obscured
  - Focus outline appears on all interactive elements:
    * Filter buttons
    * Pharmacy dropdown
    * Links (if present)
    * Any other buttons
And focus indicators are consistent in style and color
And focus indicator does not obscure the element itself
And focus indicator is clearly distinguishable from hover/active states
And focus is visually tracked as user navigates page
```

### Navigation

**Scenario 7: Form controls and interactive regions have proper labels**

```gherkin
Given the dashboard filters and controls are displayed
When a screen reader announces the page
Then all form controls are properly labeled:
  - Date range buttons have aria-label:
    * "Select 7 day period" for 7 Days button
    * "Select 30 day period" for 30 Days button
    * "Select 90 day period" for 90 Days button
    * "Select all time period" for All-time button
  - Active button has aria-pressed="true"
  - Inactive buttons have aria-pressed="false"
  - Pharmacy dropdown has aria-label: "Select pharmacy"
  - Dropdown has aria-expanded="true" when open, "false" when closed
  - Dropdown has aria-haspopup="listbox"
  - Dropdown list items have role="option"
  - Selected item has aria-selected="true"
  - Filter status text has role="status" for dynamic updates
  - Metric card regions have aria-label:
    e.g., aria-label="CAS Consultations metric card, 1,234 consultations completed"
And all labels are meaningful and descriptive
And abbreviations are expanded or defined
And screen reader announcements are clear and concise
```

**Scenario 8: Dynamic content updates are announced to screen readers**

```gherkin
Given the user changes a filter (e.g., selects a new date range)
When the metric cards are updated
Then screen reader users are notified:
  - An aria-live="polite" region announces:
    "Metrics updated. Now showing Last 7 days data for [Pharmacy Name]"
  - aria-live region has role="status"
  - Announcement is delivered within 500ms of filter change
  - Announcement does not interrupt screen reader's current speech
  - Loading state is announced (if data takes time to load):
    "Loading metrics, please wait"
  - Completion is announced:
    "Metrics loaded successfully"
  - Errors are announced:
    "Error: Unable to load metrics. Please try again."
And all status announcements are concise (< 150 characters)
And announcements use polite (not assertive) tone
And screen reader users are not required to manually refresh or reload
```

### Validation

**Scenario 9: Page passes automated accessibility testing**

```gherkin
Given the dashboard HTML and CSS are complete
When automated accessibility tests are run
Then the page passes:
  - Axe DevTools audit (0 critical and high-severity violations)
  - WAVE accessibility checker (0 errors, minimal warnings)
  - Lighthouse accessibility audit (score 90+)
  - WCAG 2.1 AA validator (no violations)
And specific passes include:
  - All images have alt text or aria-label
  - All form fields have labels
  - All interactive elements are keyboard accessible
  - Color contrast passes
  - Heading hierarchy is correct
  - No duplicate IDs exist
  - Landmark regions are properly used
  - ARIA attributes are valid
And manual testing confirms automated test results
```

### Accessibility

**Scenario 10: WCAG 2.1 AA Level compliance verified with screen reader testing**

```gherkin
Given the dashboard is complete
When tested with screen readers (NVDA, JAWS, VoiceOver)
Then the experience meets WCAG 2.1 AA requirements:
  - Page title is announced on load: "Reporting Dashboard - Choose Pharmacy"
  - Page structure is navigable by landmarks:
    * "Navigation (main)"
    * "Region: Dashboard Filters"
    * "Main"
    * "Region: Pharmacy Summary"
    * "Content info (footer)"
  - All headings are announced with level (h1, h2, h3)
  - Metric cards are announced with proper context:
    "Article: CAS Consultations metric card, heading level 3,
     1,234 consultations completed, Last 30 days,
     Trend up, focus to interact"
  - Filter state is announced:
    "30 Days, button, pressed, focus to change"
    "Pharmacy, Cardiff Pharmacy - ODS123, expandable menu"
  - Focus movement is predictable and in logical order
  - Form inputs are announced with their labels
  - Error messages are announced immediately
  - Status updates are announced with aria-live regions
  - No essential information is hidden from screen readers
And testing is performed with:
  - NVDA (Windows)
  - JAWS (Windows)
  - VoiceOver (macOS/iOS)
And all critical user tasks are completable with screen reader:
  - Navigating to dashboard
  - Understanding current filters
  - Changing date range filter
  - Changing pharmacy filter
  - Reading metric values and trends
  - Viewing pharmacy summary
And no screen reader user is blocked or confused
```

---

## Definition of Done

- [ ] Semantic HTML implemented (landmarks, headings, structure)
- [ ] All interactive elements are keyboard accessible
- [ ] Focus indicators are visible and meet contrast standards
- [ ] Tab order is logical and tested
- [ ] All form controls have proper labels and ARIA attributes
- [ ] aria-live regions implement status announcements
- [ ] aria-pressed and aria-expanded used on buttons/dropdowns
- [ ] Color contrast verified (4.5:1 for normal text, 3:1 for UI)
- [ ] Alt text and aria-labels on all images/icons
- [ ] Page passes Axe DevTools audit (0 critical violations)
- [ ] Page passes WAVE checker
- [ ] Lighthouse accessibility score 90+
- [ ] Screen reader testing completed (NVDA, JAWS, VoiceOver)
- [ ] Keyboard-only navigation tested end-to-end
- [ ] Focus management tested at all breakpoints
- [ ] All acceptance criteria passing (10 scenarios)
- [ ] WCAG 2.1 AA compliance verified
- [ ] Accessibility statement added to footer (link to accessibility policy)
- [ ] Code review passed
- [ ] Documentation of accessibility features completed
- [ ] Accessible to all users (visual, motor, cognitive, auditory)

---

## Technical Notes

### Semantic HTML5 Elements
```html
<header role="banner">
  <h1>Reporting Dashboard</h1>
  <p>Overview of consultations and registrations</p>
</header>

<main role="main">
  <section aria-label="Dashboard Filters">
    <h2>Filter Data</h2>
    <!-- Filter controls -->
  </section>

  <section aria-label="Dashboard Metrics">
    <article role="article" aria-label="CAS Consultations metric">
      <h3>CAS Consultations</h3>
      <p>1,234</p>
      <span aria-label="Trend up">↑</span>
    </article>
  </section>

  <section role="region" aria-label="Pharmacy Summary">
    <h2>Pharmacy Summary</h2>
  </section>
</main>

<footer role="contentinfo">
  <!-- Footer content -->
</footer>
```

### ARIA Attributes for Buttons
```html
<!-- Date range button (active) -->
<button aria-label="Select 30 day period" aria-pressed="true">30 Days</button>

<!-- Date range button (inactive) -->
<button aria-label="Select 7 day period" aria-pressed="false">7 Days</button>

<!-- Pharmacy dropdown (closed) -->
<select aria-label="Select pharmacy" aria-expanded="false" aria-haspopup="listbox">
  <option aria-selected="true">Cardiff Pharmacy</option>
  <option>Swansea Pharmacy</option>
</select>
```

### aria-live Region for Status Updates
```html
<div aria-live="polite" role="status" id="status-message"></div>

<!-- JavaScript update -->
document.getElementById('status-message').textContent = 
  'Metrics updated. Now showing Last 7 days data for Cardiff Pharmacy';
```

### Keyboard Event Handling
```javascript
// Dropdown arrow key navigation
dropdown.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowDown') {
    focusNextOption();
  } else if (e.key === 'ArrowUp') {
    focusPreviousOption();
  } else if (e.key === 'Escape') {
    closeDropdown();
  }
});

// Button activation
button.addEventListener('keydown', (e) => {
  if (e.key === 'Enter' || e.key === ' ') {
    e.preventDefault();
    button.click();
  }
});
```

### Accessibility Testing Tools
- **Axe DevTools:** https://www.deque.com/axe/devtools/
- **WAVE:** https://wave.webaim.org/
- **Lighthouse:** Built into Chrome DevTools
- **NVDA:** https://www.nvaccess.org/download/
- **JAWS:** https://www.freedomscientific.com/products/software/jaws/
- **VoiceOver:** Built into macOS/iOS

---

## Related Standards and Documentation

- **WCAG 2.1 AA:** https://www.w3.org/WAI/WCAG21/quickref/
- **ARIA Authoring Practices Guide:** https://www.w3.org/WAI/ARIA/apg/
- **Semantic HTML5:** https://www.w3.org/TR/html5/
- [COMPONENTS.md](../COMPONENTS.md) — Accessible components
- [UI-Template.md](../templates/UI-Template.md) — UI story guidelines
- **DHCW Design System V2:** [Accessibility Guidelines](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)
