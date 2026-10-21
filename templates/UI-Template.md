# UI User Story Template

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy user  
**I want** [what they want to do]  
**So that** [the business value/outcome]

### Context
[Set the scene. Why does this story matter? What problem does it solve? Include any relevant background or journey context.]

### Out of Scope
- [What is explicitly NOT included in this story]
- [Keep dev/QA aligned on boundaries]

### Related Stories
Stories this story navigates to or interacts with (already developed or developed in parallel):
- [Story ID]: [Story Title] — relates to [brief description of relationship]
- [Story ID]: [Story Title]

### Dependent Stories
Stories that depend on this story being complete before development can start:
- [Story ID]: [Story Title] — blocks [brief description of what it blocks]
- [Story ID]: [Story Title]

### Captions (Consultation Journey Stories Only)
**Required for:** Stories part of a multi-step consultation pathway (e.g., CCM, CAS, Vaccination journeys).

**Purpose:** High-level contextual captions orient users to their position in the consultation journey.

**Format:**
- **Journey step caption:** The workflow phase (e.g., "Clinical Conditions Management", "CAS Supply Pathway")
- **Page caption:** The specific page/outcome within that step (e.g., "Outcome: Supply", "Select outcome", "Record assessment")

**Example:**
```
Journey step: Clinical Conditions Management
Page caption: Outcome: Supply
```

**In Acceptance Criteria:** Include captions in Scenario 1 (Page Structure & Content) as displayed elements, e.g., `Then the page displays: Journey context: "Clinical Conditions Management" And page heading: "Outcome: Supply"`

---

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

**Error messaging standard:** [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md)

List each DHCW component used by the story and link to the DHCW Design System V2 reference listed in `COMPONENTS.md`. Do not introduce an unlisted component without confirming it against `COMPONENTS.md`.

---

## Acceptance Criteria

**Total Scenarios: 6**

### Page Structure & Content

**Scenario 1: [Specific title - what is displayed on page load]**

```gherkin
Given the user navigates to [page name]
When the page loads
Then the following elements are displayed:
  - [Element 1]: [description]
  - [Element 2]: [description]
  - [Element 3]: [description]
And the [element] contains the text "[specific text]"
And the layout is [description of layout]
```

### Default State

**Scenario 2: [Specific title - initial appearance and values]**

```gherkin
Given the user navigates to [page name]
When the page loads for the first time
Then [specific element] displays [default value]
And [specific element] is [state: enabled/disabled/selected]
And the page shows [description]
```

### User Interaction

**Scenario 3: [Specific title - user action and result]**

```gherkin
Given the user is on [page name]
And [precondition]
When the user [action]
Then [element] changes to [state]
And [visual feedback] is displayed
And the selection is persisted
```

### Navigation

**Scenario 4: [Specific title - navigation flow]**

```gherkin
Given the user has completed [action] on [page name]
When the user clicks [button/link]
Then the user is navigated to [next page]
And the [data] is passed to [next page]
And the user's selections are retained
```

### Validation

**Scenario 5: [Specific title - validation error handling]**

```gherkin
Given the user is on [page name]
When the user [performs invalid action]
Then an error message displays: "[Exact error message from standards/UI-ERROR-MESSAGING.md]"
And the error is displayed near [element location]
And the error is styled with: [color: red / icon: warning]
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the form/page remains on [current page]
And focus returns to [field name]
And no data is persisted
```

**Note:** All error messages MUST follow the standardized format defined in [`standards/UI-ERROR-MESSAGING.md`](../standards/UI-ERROR-MESSAGING.md).

### Accessibility

**Scenario 6: [Specific title - WCAG 2.1 AA compliance]**

```gherkin
Given the page is rendered
When a user navigates using a keyboard
Then all interactive elements are [focusable/accessible]
And the tab order is logical: [1st → 2nd → 3rd]
And screen reader announces: "[aria-label/role]"
And colour contrast ratio is at least 4.5:1
And all images have alt text: "[description]"
```

---

## Acceptance Criteria Formatting Guidelines

To ensure consistent formatting across all UI stories:

✅ **Scenario Titles:** Always in **bold** with sequential number and colon
   - Example: `**Scenario 1: Display page on initial load**`

✅ **Gherkin Code Blocks:** Use triple backticks with `gherkin` language identifier
   - Opening: `` ```gherkin ``
   - Closing: `` ``` ``
   - No indentation within the block

✅ **Multiple Conditions:** Use blank lines between separate Given/When/Then blocks within same scenario

✅ **References:** See [`guides/GHERKIN-FORMAT.md`](../guides/GHERKIN-FORMAT.md) → "Markdown Formatting for Story Files" for detailed formatting rules

---

## Notes
[Any additional context, design links, known issues, or technical considerations]
