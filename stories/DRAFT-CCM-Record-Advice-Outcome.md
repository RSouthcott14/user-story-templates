# UI: CCM - Record Advice Outcome

**Story Type:** UI  
**Tags:** AI Generated, Review (BR)

---

## Description

### Story Card

A pharmacy user has selected the "Advice only" outcome on the CCM Outcome Selection page. They now need to record the specific advice given to the patient on this dedicated page.

**Acceptance Criteria:** 12 Gherkin scenarios covering page structure, default state, user interaction, navigation, validation, and accessibility.

### Context

This page is the final step in the Clinical Conditions Management (CCM) "Advice only" consultation pathway. It appears after the user selects "Advice only" outcome on the Outcome Selection page (#603150) instead of "Refer patient".

The page contains a single textarea field for recording advice given to the patient during the consultation. This is a simpler outcome path compared to the referral pathway, which requires destination, timescale, and multiple information fields.

**Related pages:**
- **Previous:** Outcome Selection page (#603150) - "Advice only" option leads here
- **After submission:** Patient overview or consultation summary (navigation destination TBD)

### Captions

**Journey step:** Clinical Conditions Management  
**Page caption:** Outcome: Advice

### Out of Scope

- Advice templates or predefined suggestions
- Character limit enforcement beyond 2000 characters (hard stop at input level)
- Automatic advice population from other consultation fields
- Multi-language or accessibility templates
- Integration with external advice/guidance systems
- Print or export functionality for advice text

### Related and Dependent Stories

| Story ID | Title | Relationship | Details |
|----------|-------|--------------|---------|
| 603150 | UI: CCM - Outcome Selection (Limited Options) | Related | Previous step in CCM workflow; "Advice only" selection navigates to this story |
| 603156 | UI: CCM - Sensitive Health Information | Related | Referral pathway only; not reached from this story |

---

## Design Reference

| Component | DHCW Design System V2 | Notes |
|-----------|----------------------|-------|
| Page heading (h2) | [Headings - h2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=1582-16391) | "Outcome: Advice" |
| Textarea with character counter | [Textarea](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | 2000 character limit, live counter |
| Secondary button | [Button - Secondary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Previous" |
| Primary button | [Button - Primary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Save and continue" |
| Action link | [Link - Action](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Cancel and exit" |
| Error summary | [Error Summary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | role="alert", aria-live="polite" |

---

## Acceptance Criteria

**Total Scenarios: 10**

### Page Structure & Content

**Scenario 1: Page loads with advice textarea and navigation buttons**

```gherkin
Given the user has selected "Advice only" on the Outcome Selection page (#603150)
When the page loads
Then the page displays:
  - Journey caption: "Clinical Conditions Management"
  - Page heading: "Outcome: Advice"
  - Section heading: "Advice to patient"
  - Textarea labeled "Advice given to patient" with character counter showing "You have 2000 characters remaining"
  - Secondary button labeled "Previous"
  - Primary button labeled "Save and continue"
  - Action link labeled "Cancel and exit"
And the form layout uses maximum section width with equal margins
```

### Default State

**Scenario 2: Textarea is empty and ready for user input on initial page load**

```gherkin
Given the user navigates to the Record Advice Outcome page
When the page loads for the first time
Then the textarea field is empty
And the character counter displays "You have 2000 characters remaining"
And the "Save and continue" button is enabled and clickable
And the form is ready for user input
```

### User Interaction

**Scenario 3: User enters advice text and character counter updates in real-time**

```gherkin
Given the user is on the Record Advice Outcome page
When the user enters text in the "Advice given to patient" textarea
Then the text is recorded as the user types
And the character counter updates in real-time showing: "You have X characters remaining"
And the textarea text wraps automatically and scrolls if content exceeds visible area
```

**Scenario 4: Advice data persists when user navigates to another patient tab and returns**

```gherkin
Given the user has entered advice text on this page
When the user navigates to another patient tab
And subsequently returns to this consultation within the same patient encounter
Then the previously entered advice text is displayed
And the character counter displays the correct remaining character count
And the consultation model retains all stored advice data
```

**Scenario 5: User completes advice and clicks Save and continue - data stored and navigates to next page**

```gherkin
Given the user is on the Record Advice Outcome page
And has entered text in the "Advice given to patient" textarea (mandatory field)
When the user clicks the "Save and continue" button
Then the form is submitted
And the advice text is persisted in the consultation model
And the user navigates to the next page in the consultation workflow
And the advice data is accessible to subsequent pages
And the navigation completes successfully
```

**Scenario 6: User can submit with only optional advice entered or with empty textarea**

```gherkin
Given the user is on the Record Advice Outcome page
When the user leaves the textarea empty or partially filled
And clicks "Save and continue"
Then submission is permitted
And the consultation is recorded (with empty or partial advice as entered)
And the user progresses to the next page
```

### Navigation & State Management

**Scenario 7: User clicks Previous button - returns to Outcome Selection page with data retained**

```gherkin
Given the user is on the Record Advice Outcome page
And has entered advice text
When the user clicks the "Previous" button
Then the user is navigated back to the Outcome Selection page (#603150)
And the "Advice only" outcome selection is still selected on that page
And the consultation model retains the advice text entered on this page
And when the user navigates forward again to this page
Then all previously entered advice is displayed
```

**Scenario 8: User clicks Cancel and exit - navigates to Patient overview without persisting data**

```gherkin
Given the user is on the Record Advice Outcome page
And has entered advice text (but not yet submitted)
When the user clicks the "Cancel and exit" link
Then the user is navigated to the Patient overview page
And no data entered on this page is persisted in the consultation model
And the consultation is not saved
```

### Validation & Error Handling

**Scenario 9: User enters text exceeding 2000-character limit - character limit is enforced**

```gherkin
Given the user is on the Record Advice Outcome page
When the user enters text in the "Advice given to patient" textarea
And the text approaches or exceeds 2000 characters
Then the character counter updates in real-time showing: "You have X characters remaining"

When the user attempts to enter additional characters beyond 2000
Then the user is prevented from entering additional characters
And the character counter displays: "You have 0 characters remaining"
And if character limit is exceeded on submission attempt:
  - An error summary displays at the top of page with role="alert"
  - Inline error message displays near the textarea
  - Focus moves to the error summary
  - No data is persisted
And the error remains visible until the user corrects the field
```

### Accessibility

**Scenario 10: Page meets WCAG 2.1 AA accessibility compliance standards**

```gherkin
Given the Record Advice Outcome page is rendered
When a user navigates using keyboard and screen reader
Then all interactive elements are focusable via Tab key
And the tab order is logical and follows:
  "Previous" → "Advice given to patient" textarea → "Save and continue" → "Cancel and exit"

And the page heading is announced as: "Outcome: Advice" (heading level 2)
And the journey caption is announced as: "Clinical Conditions Management" (page context)
And the textarea label is associated with the field
And the character counter is announced with aria-live="polite": "You have X characters remaining"
And the "Previous" button is announced as: "Previous, button"
And the "Save and continue" button is announced as: "Save and continue, button"
And the "Cancel and exit" link is announced as: "Cancel and exit, link"

And color contrast ratio is at least 4.5:1 for all text (including buttons, labels)
And focus indicators are visible on all interactive elements
And the textarea label is properly associated with input using <label> or aria-labelledby
And the page is functional when zoomed to 200%
And textarea supports user resizing and scrolling
And error messages are announced with aria-live for screen reader users
```

---

## Definition of Done

- [ ] All acceptance criteria scenarios pass (manual or automated testing)
- [ ] Page structure matches DHCW Design System V2 components exactly
- [ ] Textarea displays with character counter showing "You have 2000 characters remaining"
- [ ] Previous button styled as Secondary button
- [ ] Save and continue button styled as Primary button
- [ ] Cancel and exit displays as Action Link
- [ ] Textarea is empty on initial page load
- [ ] Character counter updates in real-time as user types
- [ ] Character limit of 2000 characters is enforced (user cannot enter beyond limit)
- [ ] Entered text is immediately visible
- [ ] Advice data persists when user navigates to another patient tab and returns
- [ ] Advice data persists when user clicks Previous and returns to page
- [ ] Advice field accepts any text input (no format validation)
- [ ] Error summary displays at top of page with role="alert" and aria-live="polite" when errors occur
- [ ] Error summary focus management: Focus automatically moves to error summary when validation error occurs
- [ ] Error summary remains focusable for keyboard users to review all errors
- [ ] WCAG 2.1 AA accessibility compliance verified
- [ ] Color contrast ratio 4.5:1 or higher for all text elements
- [ ] Tab order follows defined sequence: Previous → Textarea → Save and continue → Cancel and exit
- [ ] Page is functional when zoomed to 200%
- [ ] Keyboard navigation works without mouse
- [ ] Screen reader announcements are clear and accurate
- [ ] Character counter announces updates to screen readers (aria-live="polite")

---

## Notes

**Navigation flow:**
- **Previous button:** Returns to #603150 (Outcome Selection) with "Advice only" selection and advice data retained
- **Save and continue:** Stores advice in consultation model and navigates forward (destination TBD—awaiting feature workflow definition)
- **Cancel and exit:** Navigates to Patient overview without persisting any consultation data

**Data persistence:**
- Advice is stored in the consultation model only when "Save and continue" is clicked
- Users can abandon changes by clicking "Cancel and exit" without affecting consultation state
- Data persists in session when navigating to other patient tabs and returning (session-scoped storage)

**Character counter behavior:**
- Updates dynamically as user types (aria-live="polite" for screen reader announcements)
- Enforces 2000-character maximum at input level (user prevented from typing beyond limit)
- Displays "You have 0 characters remaining" when limit reached
- Counter updates accurately when text is deleted

**Optional field:** The advice field is effectively optional—users can submit with empty or partial text. The page does not require validation of content presence, only character limit enforcement.

**Comparison to referral outcome page:** This page is a simpler variant with a single textarea instead of multiple form fields. Unlike the referral outcome page which requires destination and timescale selections, this page only captures free-text advice.
