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

**Total Scenarios: 11**

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
When the user clicks the "Refer patient" radio button
Then the "Refer patient" option is selected (radio button filled)
And the "Advice only" option is deselected
And the selection is immediately visible with visual feedback
```

**Scenario 4: User selects "Advice only" outcome**

```gherkin
Given the user is on the outcome selection page
When the user clicks the "Advice only" radio button
Then the "Advice only" option is selected (radio button filled)
And the "Refer patient" option is deselected
And the selection is immediately visible with visual feedback
```

**Scenario 5: Selected outcome persists when user returns to page within same journey**
navigates back to page within same journey**

```gherkin
Given the user has selected an outcome ("Refer patient" or "Advice only")
And the user has clicked Continue and navigated to the next page (outcome now stored in consultation model)
When the user navigates back to the outcome selection page within the same consultation journey
Then the previously selected outcome remains selected in the radio button group
And the consultation model retains the stored outcome valuetient" and "Advice only" outcomes
```

### Navigation

**Scenario 6: User selects "Refer patient" and clicks Continue - outcome stored and navigates to Referral Outcome page**

```gherkin
Given the user has selected "Refer patient"
When the user clicks the Continue button
Then the selected outcome ("Refer patient") is stored in the consultation model
And the user navigates to the Record Referral Outcome page (603161)
And the stored outcome is accessible to the next page
And the navigation completes successfully
```

**Scenario 7: User selects "Advice only" and clicks Continue - outcome stored and navigates to Advice Outcome page**

```gherkin
Given the user has selected "Advice only"
When the user clicks the Continue button
Then the selected outcome ("Advice only") is stored in the consultation model
And the user navigates to the Record Advice Outcome page (603162)
And the stored outcome is accessible to the next page
And the navigation completes successfully
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

**Scenario 9: Consultation remains active when user navigates to another patient tab and returns**

```gherkin
Given the user has an active consultation in progress with an outcome selected
And the outcome is stored in the consultation model
When the user navigates to another patient tab (to check patient information)
And subsequently returns to the consultation within the same patient encounter
Then the consultation shall remain active
And this outcome selection page is displayed
And the previously selected outcome remains selected in the radio button group
And the consultation model state is preserved
```

### Validation

**Scenario 10: User attempts to continue without selecting an outcome - comprehensive validation error handling**

```gherkin
Given the user is on the outcome selection page
And no outcome has been selected
When the user clicks the Continue button
Then an error message displays: "Select consultation outcome"
And the error appears in two locations:
  - In an error summary box at the top of the page (role="alert", aria-live="polite")
  - Near the radio button group as inline error text
And both error instances are styled with:
  - Red text color (DHCW error color palette)
  - Warning icon
  - Clear visual distinction from instructional text
And the error summary is focusable and keyboard accessible
And focus is automatically moved to the error summary at the top of the page
And no navigation occurs
And the user remains on the current page
And no data is persisted in the consultation model
And the error remains visible until the user makes a valid selection
```

### Accessibility

**Scenario 11: Page meets WCAG 2.1 AA compliance standards**

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
- [ ] Selected outcome is stored in the consultation model when user clicks Continue (not on initial radio selection)
- [ ] Visual feedback on selection is immediate (radio button fills, deselected state updates)
- [ ] Validation error message displays with correct text: "Select consultation outcome"
- [ ] Error summary box displays at top of page with role="alert" and aria-live="polite"
- [ ] Error summary is focusable and keyboard accessible
- [ ] Inline error message displays near the radio button group
- [ ] Both error instances styled with red text, warning icon, and DHCW error color palette
- [ ] **Error summary focus management:** Focus automatically moves to error summary at top of page when validation error occurs
- [ ] Focus remains in error summary (or returns to it) until user makes a valid selection
- [ ] No data is persisted when validation error occurs
- [ ] Navigation to 603161 (Refer patient path) works correctly with outcome stored and passed
- [ ] Navigation to 603162 (Advice only path) works correctly with outcome stored and passed
- [ ] Previous button returns to prior journey step with outcome retained
- [ ] Consultation state persists when user navigates away to another patient tab and returns
- [ ] Selected outcome remains active and page displays correctly after patient tab navigation
- [ ] WCAG 2.1 AA accessibility compliance verified (keyboard, screen reader, color contrast, focus indicators)
- [ ] Tab order confirmed: Previous → "Refer patient" → "Advice only" → Continue
- [ ] Screen reader announcements match specified text in accessibility scenario (Scenario 11)
- [ ] Component styling and layout tested on mobile, tablet, and desktop viewports
- [ ] Error message styling consistent with standards/UI-ERROR-MESSAGING.md
- [ ] Code review completed and approved by tech lead
- [ ] Story linked to parent feature in Azure DevOps (Feature ID required)

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
- **Data Persistence Timing:** Consultation model state is updated **when user clicks Continue** (after selection, before navigation to next page) — NOT immediately on radio selection
  - Visual feedback on selection is immediate (radio button fills)
  - Consultation model storage is deferred until Continue action
  - This ensures outcome is only committed when user confirms they want to proceed
- The selected outcome should be accessible and persisted throughout the consultation lifecycle for both forward navigation (Continue) and backward navigation (Previous)
- **Error Message Handling:**
  - Error message: "Select consultation outcome" (follows standardized format from `standards/UI-ERROR-MESSAGING.md`)
  - Error summary displays at top of page with `role="alert"` and `aria-live="polite"` for screen reader announcement
  - Inline error displays near radio button group
  - **Focus Management:** Focus automatically moves to the error summary when validation error occurs (not to the radio button group)
  - Error is cleared/hidden immediately when user selects a valid option
- Both outcome options must be equally prominent (no visual bias toward "Refer patient")
- **Consultation State Persistence:** When user is in active consultation, navigates to another patient tab, and returns to the same encounter, the consultation and outcome selection must remain active and visible
- Consultation model must be thread-safe for concurrent outcome updates (if applicable)

### Known Considerations
- Outcome selection must not be lost if user navigates backward and returns
- Consultation model must be thread-safe for concurrent outcome updates
- Both outcome options must be equally prominent (no default bias toward "Refer patient")
