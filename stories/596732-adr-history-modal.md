# Adverse Reaction History Modal

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to view the complete history of an adverse reaction record with each version displayed as an expandable accordion  
**So that** I can understand how the adverse reaction information has evolved over time, review previous symptom recordings, and see who made updates and when

### Context
Currently, the adverse reaction modal displays only the current version of a record. Pharmacy users need visibility into the full history of adverse reaction changes to understand the clinical progression, verify data consistency, and audit who made updates. The accordion pattern allows users to review the entire history without overwhelming the modal with excessive scrolling by supporting single-open behavior.

### Out of Scope
- Reverting or restoring previous ADR versions (history is read-only)
- Editing historical records (only the current version can be edited in a separate edit dialog)
- Exporting or printing ADR history
- Filtering or searching history by date range or criteria
- Pagination or lazy-loading of history (all versions load on modal open)
- Side-by-side comparison of two versions
- User annotations or comments on individual historical entries
- Real-time synchronization if another user updates the ADR while this modal is open

### Related Stories
- **603500: Adverse Reaction Management** — Feature context; this modal provides history UI for the ADR feature

### Dependent Stories
None at this time

---

### Design Reference
**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components Used:**
1. **Expander** — [Figma Reference](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-142) — Accordion container for each ADR version
2. **Summary List** — Display key-value ADR fields within expanded accordion
3. **Modal Container** — Contains the full accordion list
4. **Button (Close)** — Dismiss the modal

---

## Acceptance Criteria

**Total Scenarios: 7**

### Page Structure & Content

**Scenario 1: Modal displays ADR history with accordion structure**

```gherkin
Given the user opens the adverse reaction history modal for a reaction
When the modal loads
Then the modal title displays: "Adverse reaction history: [reaction name]"
And the modal displays a list of accordion containers
And each accordion heading displays: "Last updated: [date]" (formatted as "11 Jun 2025")
And the accordions are ordered with the most recent version at the top
And a "Close" button is displayed at the bottom of the modal
And the layout follows the DHCW modal pattern with appropriate spacing
```

### Default State

**Scenario 2: Most recent ADR version is auto-expanded on modal load**

```gherkin
Given the user opens the adverse reaction history modal
When the modal loads for the first time
Then the most recent accordion (top entry) is automatically expanded
And all other accordions are collapsed
And the expanded accordion displays a minus (−) icon in its heading
And the collapsed accordions display a plus (+) icon in their headings
And the expanded accordion shows the following ADR details:
  - Type of reaction
  - Anaphylaxis observed
  - Date of last reaction
  - Date of first reaction (if recorded)
  - Symptoms and signs
  - Reaction observed by a clinician
  - Notes
  - Last updated by (name and ID)
```

### User Interaction

**Scenario 3: User expands a closed accordion to view older ADR version**

```gherkin
Given the user is viewing the adverse reaction history modal
And an accordion for a previous version is currently collapsed
When the user clicks the collapsed accordion heading or its toggle icon
Then that accordion expands to reveal its ADR details
And the previously open accordion automatically closes
And the newly opened accordion displays a minus (−) icon
And the previously open accordion now displays a plus (+) icon
And the page does not scroll to bring the newly opened accordion into view if already visible
```

**Scenario 4: User collapses an expanded accordion**

```gherkin
Given the user is viewing an expanded accordion in the history modal
When the user clicks the expanded accordion heading or its toggle icon
Then the accordion collapses
And the accordion heading now displays a plus (+) icon
And no other accordions are affected
And the collapse happens without page refresh
```

### Navigation

**Scenario 5: User closes the modal**

```gherkin
Given the user is viewing the adverse reaction history modal
When the user clicks the "Close" button
Then the modal closes
And the user returns to the previous page or view
And the accordion expansion state is not persisted (next time the modal opens, the most recent version auto-expands)
```

### Validation & Error Handling

**Scenario 6: Modal handles missing or incomplete ADR history gracefully**

```gherkin
Given an adverse reaction has only one version (no history)
When the user opens the adverse reaction history modal
Then a single accordion is displayed showing that version
And the accordion is automatically expanded
And the user can close the accordion without it being replaced by another entry
And if any ADR field is missing, it is not displayed in the accordion

Given the history data fails to load from the server
When the user attempts to open the adverse reaction history modal
Then an error message displays: "Unable to load adverse reaction history. Please refresh the page and try again."
And a "Close" button remains available
And no data is persisted
```

### Accessibility

**Scenario 7: Modal meets WCAG 2.1 AA accessibility standards**

```gherkin
Given the user is using a keyboard to navigate the adverse reaction history modal
When the modal loads
Then focus is placed on the first accordion heading (most recent)
And the tab order is: First accordion heading → Subsequent accordion headings → Close button
And pressing Enter or Space on an accordion heading expands or collapses it
And pressing Escape closes the modal

Given the user is using a screen reader
When the user navigates to an accordion heading
Then the screen reader announces: "[Date] [status]" (e.g., "11 Jun 2025 button expanded")
And when the user expands an accordion, the screen reader announces: "[Date] expanded, details follow"
And when the user collapses an accordion, the screen reader announces: "[Date] collapsed"
And all ADR field labels are read clearly (e.g., "Type of reaction: Allergy")
And the Close button is announced as: "Close button"

Given the user is viewing the modal on a small screen
Then the accordion list remains readable without horizontal scrolling
And each accordion heading and content are fully accessible
And the minimum font size is 14px for body text and 16px for labels
And the contrast ratio for text and icons meets WCAG AA standards (4.5:1 for normal text, 3:1 for icons)
```

---

## Out of Scope Summary
- ❌ Reverting to previous versions
- ❌ Editing historical records
- ❌ Exporting or printing history
- ❌ Filtering or searching by date
- ❌ Pagination or lazy-loading
- ❌ Side-by-side version comparison
- ❌ User annotations on versions
- ❌ Real-time sync notifications

---

## Verification Checklist

- [ ] Component: Expander component from DHCW Design System V2 is used for accordion pattern
- [ ] Component: Modal follows DHCW modal standard for layout, sizing, and spacing
- [ ] Behavior: Most recent ADR version auto-expands on every modal open
- [ ] Behavior: Only one accordion can be open at a time (single-open pattern)
- [ ] Behavior: Switching accordion closes the previously open one seamlessly
- [ ] Content: All ADR fields are displayed correctly in expanded accordions
- [ ] Content: Dates are formatted consistently (e.g., "11 Jun 2025")
- [ ] Interaction: Keyboard navigation works (Tab, Enter, Space, Escape)
- [ ] Accessibility: Screen reader support for expand/collapse state changes
- [ ] Accessibility: Contrast ratios meet WCAG AA standards
- [ ] Error Handling: Graceful handling of missing or incomplete data
- [ ] Error Handling: User-friendly error message if data fails to load
- [ ] UX: Modal does not require scroll when accordion is collapsed on small screens
- [ ] UX: Close button is always visible and functional
