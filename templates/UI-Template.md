# UI User Story Template

## Story Type: UI

---

## Description

### Story Card
**As a** [user role]  
**I want** [what they want to do]  
**So that** [the business value/outcome]

### Context
[Set the scene. Why does this story matter? What problem does it solve? Include any relevant background or journey context.]

### Out of Scope
- [What is explicitly NOT included in this story]
- [Keep dev/QA aligned on boundaries]

### Related/Dependent Stories
- [Story ID]: [Story Title] — [relationship: blocks/depends on/relates to]
- [Story ID]: [Story Title]

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Template:** [`templates/UI-Template.md`](UI-Template.md)

**Component guidance:** [`COMPONENTS.md`](../COMPONENTS.md)

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
Then an error message displays: "[specific error text]"
And the error is displayed near [element location]
And the form/page remains on [current page]
```

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

## Definition of Done

- [ ] All acceptance criteria met and validated by QA
- [ ] Code peer reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Accessibility testing completed (WCAG 2.1 AA)
- [ ] UI matches DHCW Design System V2 (Figma/mock-up)
- [ ] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [ ] Mobile responsiveness tested
- [ ] Documentation updated (if applicable)
- [ ] Ready for UAT

---

## Notes
[Any additional context, design links, known issues, or technical considerations]
