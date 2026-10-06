# 603150 | CCM Outcome Selection (Limited Options)

## Story Card

**As a** pharmacy user  
**I want to** select a consultation outcome  
**So that** the consultation record reflects how the case will be concluded

---

## Context

This page is part of the Clinical Conditions Management (CCM) consultation pathway, appearing when:
- A CCM CAS consultation has **no condition found**, OR
- A CCM IPS consultation has **Working Diagnosis = No**

The selected outcome determines the next stage in the consultation journey and is persisted in the consultation model. Users must explicitly choose between referring the patient or providing advice only before proceeding.

---

## Out of Scope

- Editing previous selections made earlier in this journey
- Handling multiple conditions or partial condition matches
- Displaying full condition history or clinical context
- Validation error recovery beyond returning focus to the outcome field
- Pre-population of outcome based on external data sources
- Saving draft outcomes without explicit Continue action

---

## Related / Dependent Stories

| Story | Relationship | Notes |
|-------|--------------|-------|
| [603161-UI-Record-Referral-Outcome](../stories/603161-UI-Record-Referral-Outcome.md) | Relates to | Navigated to when "Refer patient" is selected (already developed) |
| [603162-UI-Record-Advice-Outcome](../stories/603162-UI-Record-Advice-Outcome.md) | Relates to | Navigated to when "Advice only" is selected (already developed) |

---

## Design Reference

**DHCW Design System V2:** https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

**Components Used:**

| Component | Design System Reference | Notes |
|-----------|------------------------|-------|
| Radio Button Group | DHCW Design System V2 – Forms / Radio Buttons | Two mutually exclusive options |
| Button (Secondary) | DHCW Design System V2 – Buttons / Secondary | Previous button for journey navigation |
| Button (Primary) | DHCW Design System V2 – Buttons / Primary | Continue button for form submission |
| Page Heading | DHCW Design System V2 – Typography / Headings | "Select consultation outcome" |

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Page loads with outcome options and navigation buttons**

```gherkin
Given the user has completed the CCM condition step (no condition found or Working Diagnosis = No)
When the page loads
Then the page displays:
  - A page heading: "Select consultation outcome"
  - A radio button group with two mutually exclusive options:
    - "Refer patient"
    - "Advice only"
  - A Secondary button labeled "Previous" aligned left
  - A Primary button labeled "Continue" aligned right
And both buttons are visible and accessible
```

### Default State

**Scenario 2: No outcome is pre-selected on initial page load**

```gherkin
Given the user navigates to the outcome selection page
When the page loads for the first time
Then neither the "Refer patient" nor "Advice only" radio button is selected
And the Continue button is active and clickable
And the radio button group has focus indicator ready for keyboard navigation
```

### User Interaction

**Scenario 3: User selects "Refer patient" outcome**

```gherkin
Given the user is on the outcome selection page
When the user clicks or focuses the "Refer patient" radio button
Then the "Refer patient" option is selected (radio button filled)
And the "Advice only" option is deselected
And the selection is immediately visible with visual feedback
```

**Scenario 4: User selects "Advice only" outcome**

```gherkin
Given the user is on the outcome selection page
When the user clicks or focuses the "Advice only" radio button
Then the "Advice only" option is selected (radio button filled)
And the "Refer patient" option is deselected
And the selection is immediately visible with visual feedback
```

**Scenario 5: Selected outcome persists in consultation model across page interactions**

```gherkin
Given the user has selected an outcome ("Refer patient" or "Advice only")
When the user navigates away and returns to this page within the same journey
Then the previously selected outcome remains selected
And the consultation model retains the selected outcome value
And this behavior is consistent for both "Refer patient" and "Advice only" selections
```

### Navigation

**Scenario 6: User selects "Refer patient" and clicks Continue**

```gherkin
Given the user has selected "Refer patient"
When the user clicks the Continue button
Then the user navigates to the Record Referral Outcome page (603161)
And the selected outcome ("Refer patient") is persisted in the consultation model
And the consultation model is accessible to the next page
```

**Scenario 7: User selects "Advice only" and clicks Continue**

```gherkin
Given the user has selected "Advice only"
When the user clicks the Continue button
Then the user navigates to the Record Advice Outcome page (603162)
And the selected outcome ("Advice only") is persisted in the consultation model
And the consultation model is accessible to the next page
```

**Scenario 8: User clicks Previous button to return to prior journey step**

```gherkin
Given the user is on the outcome selection page
And has selected an outcome (e.g., "Refer patient")
When the user clicks the Previous button
Then the user returns to the previous step in the consultation journey
And the selected outcome is retained in the consultation model
And the outcome remains available if the user re-enters this page
```

### Validation

**Scenario 9: User attempts to continue without selecting an outcome - validation error displayed and focused**

```gherkin
Given the user is on the outcome selection page
And no outcome has been selected
When the user clicks the Continue button
Then an error message displays: "Select consultation outcome"
And the error appears in two locations:
  - In an error summary box at the top of the page (role="alert", aria-live="polite")
  - Near the radio button group as inline error text
And both error instances are styled with:
  - Red text color (or DHCW error color)
  - Warning icon
  - Clear visual distinction from instructional text
And focus is moved to the error summary at the top of the page
And the error summary is focusable and keyboard accessible
And no navigation occurs
And the user remains on the current page
And no data is persisted in the consultation model
And the error remains visible until the user makes a selection
```

### Accessibility

**Scenario 10: Page meets WCAG 2.1 AA compliance standards**

```gherkin
Given the outcome selection page is rendered
When a user navigates using keyboard and screen reader
Then all interactive elements are focusable via Tab key
And the tab order is logical and follows: Previous button → "Refer patient" radio button → "Advice only" radio button → Continue button
And the page heading is announced as: "Select consultation outcome" (heading level 1)
And the radio button group is announced as: "Select consultation outcome, group of radio buttons"
And each radio option is announced with its label and state: "Refer patient, radio button, not checked" (or "checked")
And the Previous button is announced as: "Previous, button"
And the Continue button is announced as: "Continue, button"
And color contrast ratio is at least 4.5:1 for all text (including buttons and error messages)
And focus indicators are visible on all interactive elements (radio buttons, buttons)
And all form labels are associated with their inputs using proper HTML aria-labelledby or label elements
And the radio button group has a visible focus state that clearly indicates keyboard navigation
```

---

## Definition of Done

- [ ] Acceptance criteria scenarios all pass (manual or automated testing)
- [ ] Page structure matches DHCW Design System V2 Radio Button Group and Button components
- [ ] Radio button group displays exactly two mutually exclusive options
- [ ] Previous and Continue buttons are properly styled as Secondary and Primary buttons
- [ ] Selected outcome is correctly persisted in the consultation model
- [ ] Validation error message displays with correct text, styling, and accessibility attributes
- [ ] Focus management returns to radio button group after validation error
- [ ] No data is persisted when validation error occurs
- [ ] Navigation to 603161 (Refer patient path) works correctly with outcome passed
- [ ] Navigation to 603162 (Advice only path) works correctly with outcome passed
- [ ] Previous button returns to prior journey step with outcome retained
- [ ] WCAG 2.1 AA accessibility compliance verified (keyboard, screen reader, color contrast, focus indicators)
- [ ] Tab order confirmed: Previous → "Refer patient" → "Advice only" → Continue
- [ ] Screen reader announcements match specified text in accessibility scenario
- [ ] Component styling and layout tested on mobile, tablet, and desktop viewports
- [ ] Error message styling consistent with standardized UI error messaging standards
- [ ] Code review completed and approved by tech lead
- [ ] Story linked to parent feature in Azure DevOps

---

## Notes

### Related Documentation
- Feature reference: Clinical Conditions Management (CCM) consultation pathway
- Consultation model: Persists outcome value throughout journey lifecycle
- Error messaging: Follows standardized UI error messaging patterns from `standards/UI-ERROR-MESSAGING.md`
- Accessibility baseline: WCAG 2.1 AA with DHCW Design System V2 compliance

### Design Links
- Figma component frame: DHCW Design System V2 – Radio Buttons and Buttons
- Consultation model architecture: Reference ADO feature documentation

### Implementation Notes
- Outcome selection is a **required field** — Continue button validation triggers on click, not on blur
- Consultation model state should be updated immediately on radio selection (optimistic update)
- Navigation should preserve the selected outcome in the consultation model for both forward and backward navigation
- Error message should be removed or hidden once a valid selection is made

### Known Considerations
- Outcome selection must not be lost if user navigates backward and returns
- Consultation model must be thread-safe for concurrent outcome updates
- Both outcome options must be equally prominent (no default bias toward "Refer patient")
