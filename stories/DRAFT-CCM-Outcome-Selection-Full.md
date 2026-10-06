# DRAFT | CCM Outcome Selection (Full)

**Feature:** 577035  
**Story Type:** UI

---

## Story Card

**As a** pharmacy user  
**I want to** select a consultation outcome from four options  
**So that** the consultation record reflects the chosen pathway and I navigate to the appropriate next step

---

## Context

This page is part of the Clinical Conditions Management (CCM) consultation pathway. It presents users with four mutually exclusive outcomes that determine the next stage in the consultation:
- **Supply** — Patient receives supply only (no further action required)
- **Supply & refer** — Patient receives supply AND is referred to another service
- **Refer** — Patient is referred to another service only
- **Advice** — Patient receives advice only

The selected outcome is stored in the consultation model and determines which page the user navigates to next. Navigation targets vary by pathway (IPS vs. CAS) for Supply and Supply & refer options.

---

## Out of Scope

- Editing previous selections made earlier in this journey
- Handling multiple conditions or partial condition matches
- Displaying full condition history or clinical context
- Validation error recovery beyond returning focus to the error summary
- Pre-population of outcome based on external data sources
- Saving draft outcomes without explicit Continue action
- Changing outcome after Continue has been clicked (that would require a separate edit journey)

---

## Related / Dependent Stories

| Story | Relationship | Notes |
|-------|--------------|-------|
| [603159-UI-IPS-Supply-Record](../stories/603159-UI-IPS-Supply-Record.md) | Relates to | Navigated to when "Supply" is selected on IPS pathway (already developed) |
| [605456-UI-CAS-Supply-Record](../stories/605456-UI-CAS-Supply-Record.md) | Relates to | Navigated to when "Supply" is selected on CAS pathway (already developed) |
| [605457-UI-IPS-Supply-Refer-Record](../stories/605457-UI-IPS-Supply-Refer-Record.md) | Relates to | Navigated to when "Supply & refer" is selected on IPS pathway (already developed) |
| [603160-UI-CAS-Supply-Refer-Record](../stories/603160-UI-CAS-Supply-Refer-Record.md) | Relates to | Navigated to when "Supply & refer" is selected on CAS pathway (already developed) |
| [603161-UI-Record-Referral-Outcome](../stories/603161-UI-Record-Referral-Outcome.md) | Relates to | Navigated to when "Refer" is selected (already developed) |
| [603162-UI-Record-Advice-Outcome](../stories/603162-UI-Record-Advice-Outcome.md) | Relates to | Navigated to when "Advice" is selected (already developed) |

---

## Design Reference

**DHCW Design System V2:** https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2

**Components Used:**

| Component | Design System Reference | Notes |
|-----------|------------------------|-------|
| Radio Button Group | DHCW Design System V2 – Forms / Radio Buttons | Four mutually exclusive options |
| Button (Secondary) | DHCW Design System V2 – Buttons / Secondary | Previous button for journey navigation |
| Button (Primary) | DHCW Design System V2 – Buttons / Primary | Continue button for form submission |
| Page Heading | DHCW Design System V2 – Typography / Headings | "What would you like to do?" |
| Error Summary | DHCW Design System V2 – Forms / Error Summary | Top-of-page error container with role="alert" |

---

## Acceptance Criteria

**Total Scenarios: 11**

### Page Structure & Content

**Scenario 1: Page displays with four consultation outcome options**

```gherkin
Given the user is on the CCM Outcome Selection (Full) page
When the page loads
Then a heading "What would you like to do?" is visible
And four mutually exclusive outcome options are displayed:
  - Supply
  - Supply & refer
  - Refer
  - Advice
And a "Previous" button (secondary style) is present
And a "Continue" button (primary style) is present
```

### Default State

**Scenario 2: No outcome pre-selected on page load**

```gherkin
Given the user navigates to the CCM Outcome Selection (Full) page
When the page loads
Then no radio button is selected by default
And the "Continue" button is enabled
```

### User Interaction

**Scenario 3: User can select a consultation outcome**

```gherkin
Given the user is on the CCM Outcome Selection (Full) page
When the user clicks the "Supply" radio button
Then the radio button is visually selected (filled)
And no data is persisted to the consultation model
```

**Scenario 4: User can change selection**

```gherkin
Given the user has selected "Supply"
When the user clicks "Supply & refer"
Then "Supply & refer" is visually selected (filled)
And "Supply" is visually deselected
And no data is persisted
```

### Navigation Paths

**Scenario 5: Supply selection navigates to correct page based on pathway**

```gherkin
Given the user has selected "Supply"
When the user clicks Continue
Then the consultation model stores outcome as "Supply"
And:
  | Pathway | Next Page |
  | IPS     | 603159    |
  | CAS     | 605456    |
```

**Scenario 6: Supply & refer selection navigates to correct page based on pathway**

```gherkin
Given the user has selected "Supply & refer"
When the user clicks Continue
Then the consultation model stores outcome as "Supply & refer"
And:
  | Pathway | Next Page |
  | IPS     | 605457    |
  | CAS     | 603160    |
```

**Scenario 7: Refer selection navigates to consistent page**

```gherkin
Given the user has selected "Refer"
When the user clicks Continue
Then the consultation model stores outcome as "Refer"
And the user navigates to page 603161
```

**Scenario 8: Advice selection navigates to consistent page**

```gherkin
Given the user has selected "Advice"
When the user clicks Continue
Then the consultation model stores outcome as "Advice"
And the user navigates to page 603162
```

### Consultation State Persistence

**Scenario 9: Selection persists across patient tab navigation**

```gherkin
Given the user has selected "Supply & refer" on the CCM Outcome Selection (Full) page
And has NOT yet clicked Continue
When the user navigates to another patient tab
And then returns to this patient
Then "Supply & refer" remains visually selected on the page
And when the user clicks Continue
Then the selection is stored in the consultation model
```

### Validation

**Scenario 10: User must select an outcome before proceeding**

```gherkin
Given the user is on the CCM Outcome Selection (Full) page
When no outcome is selected
And the user clicks Continue
Then an error message "Select consultation outcome" is displayed at the top of the page in an error summary with role="alert" and aria-live="polite"
And an inline error message appears near the radio button group
And focus is automatically moved to the error summary
And no data is persisted
And when the user selects "Advice"
Then the error message is cleared
And "Advice" remains selected
And the user can proceed by clicking Continue
```

### Accessibility

**Scenario 11: Page meets WCAG 2.1 AA accessibility standards**

```gherkin
Given the user is accessing the page with a screen reader and keyboard only
When the page loads
Then the heading "What would you like to do?" is announced
And the radio button group is focusable
And each radio option is announced with its label
And tab order is: Previous → Supply → Supply & refer → Refer → Advice → Continue
And when the user selects an outcome via keyboard (Space/Enter)
Then the selection is announced by the screen reader
And when a validation error occurs
Then the error summary receives focus automatically
And the error message is announced by the screen reader with aria-live="polite"
```

---

## Definition of Done

- [ ] Acceptance criteria scenarios all pass (manual or automated testing)
- [ ] Page structure matches DHCW Design System V2 Radio Button Group and Button components
- [ ] Radio button group displays exactly four mutually exclusive options: Supply, Supply & refer, Refer, Advice
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
- [ ] Navigation pathway logic verified:
  - [ ] "Supply" on IPS navigates to 603159
  - [ ] "Supply" on CAS navigates to 605456
  - [ ] "Supply & refer" on IPS navigates to 605457
  - [ ] "Supply & refer" on CAS navigates to 603160
  - [ ] "Refer" navigates to 603161 (both pathways)
  - [ ] "Advice" navigates to 603162 (both pathways)
- [ ] Outcomes are stored and passed to destination pages correctly
- [ ] Previous button returns to prior journey step with outcome retained
- [ ] Consultation state persists when user navigates away to another patient tab and returns
- [ ] Selected outcome remains active and page displays correctly after patient tab navigation
- [ ] All four options are equally prominent (no visual bias toward any single option)
- [ ] WCAG 2.1 AA accessibility compliance verified (keyboard, screen reader, color contrast, focus indicators)
- [ ] Tab order confirmed: Previous → Supply → Supply & refer → Refer → Advice → Continue
- [ ] Screen reader announcements match specified text in accessibility scenario (Scenario 11)
- [ ] Component styling and layout tested on mobile, tablet, and desktop viewports
- [ ] Error message styling consistent with standards/UI-ERROR-MESSAGING.md
- [ ] Code review completed and approved by tech lead
- [ ] Story linked to parent feature in Azure DevOps (Feature 577035)

---

## Notes

### Related Documentation
- Feature reference: Clinical Conditions Management (CCM) consultation pathway (Feature 577035)
- Consultation model: Persists outcome value throughout journey lifecycle
- Error messaging: Follows standardized UI error messaging patterns from `standards/UI-ERROR-MESSAGING.md`
- Accessibility baseline: WCAG 2.1 AA with DHCW Design System V2 compliance

### Design Links
- Figma component frame: DHCW Design System V2 – Radio Buttons and Buttons
- Consultation model architecture: Reference ADO feature documentation

### Implementation Notes

**Data Persistence Timing:**
- Consultation model state is updated **when user clicks Continue** (after selection, before navigation to next page) — NOT immediately on radio selection
- Visual feedback on selection is immediate (radio button fills)
- Consultation model storage is deferred until Continue action
- This ensures outcome is only committed when user confirms they want to proceed

**Navigation Logic:**
- Navigation target is determined by two factors: **outcome selection** + **current pathway (IPS/CAS)**
- Supply and "Supply & refer" outcomes have pathway-specific destinations
- Refer and Advice outcomes navigate to the same destination regardless of pathway
- The pathway context must be available in the consultation model or session state when determining next page

**Error Message Handling:**
- Error message: "Select consultation outcome" (follows standardized format from `standards/UI-ERROR-MESSAGING.md`)
- Error summary displays at top of page with `role="alert"` and `aria-live="polite"` for screen reader announcement
- Inline error displays near radio button group
- **Focus Management:** Focus automatically moves to the error summary when validation error occurs (not to the radio button group)
- Error is cleared/hidden immediately when user selects a valid option

**Consultation State Persistence:**
- When user is in active consultation, navigates to another patient tab, and returns to the same encounter, the consultation and outcome selection must remain active and visible
- Visual selection state must be preserved across patient tab navigation
- Actual model persistence occurs only on Continue click

**Component Requirements:**
- All four outcome options must be equally prominent (no visual bias)
- Radio button styling must follow DHCW Design System V2
- Error summary styling must match button and form component styling
- Accessibility features (aria-labels, role attributes) must be implemented per DHCW standards

### Known Considerations
- Outcome selection must not be lost if user navigates backward and returns (via Previous button)
- Consultation model must be thread-safe for concurrent outcome updates if applicable
- All four outcome options must be equally prominent (no default bias)
- Pathway context (IPS vs. CAS) must be accessible to navigation logic when Continue is clicked
