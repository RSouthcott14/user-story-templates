# UI Example: Clinical Scoring Tool Selection

## Story Type: UI | Feature: 577033 | US: 585587

---

## Description

### Story Card
**As a** Clinical Assessment Specialist  
**I want** to select a clinical scoring tool from available options  
**So that** I can conduct the most appropriate clinical assessment for the patient's condition

### Context
This story is part of the Clinical Conditions Management (CCM) journey. After the user determines which clinical condition they're assessing, they must select from available scoring tools. Different conditions support different assessment methodologies (e.g., FourMats or Centor scoring for certain conditions). This selection drives the assessment flow and scoring logic.

**Related Journey Step**: Clinical Assessment → Tool Selection → Assessment Completion

### Out of Scope
- Custom tool creation
- Tool configuration or setup
- Historical tool usage analytics
- Tool documentation or help content (separate accessibility story)

### Related/Dependent Stories
- US 585586: Display available clinical conditions (upstream dependency)
- US 585588: Conduct clinical assessment using selected tool (downstream dependency)
- Feature 577033: Clinical Conditions Management (parent feature)

---

## Acceptance Criteria

### Scenario 1: Page Structure & Content
**Title**: Tool selection page displays all available options

```gherkin
Given the user has selected a clinical condition
When they navigate to the tool selection page
Then the following elements are displayed:
  - Page heading: "Select Assessment Tool"
  - Condition name confirmation: "[Selected Condition]"
  - Radio button group with all available tools
  - Tool descriptions/labels for each option
  - Continue button (initially enabled)
  - Back button to return to condition selection
And each tool option displays:
  - Radio button control
  - Tool name (e.g., "FourMats" or "Centor")
  - Brief description: "[Tool description]"
  - Icon or visual indicator (if in design)
```

### Scenario 2: Default State
**Title**: Page loads with no pre-selection

```gherkin
Given the user navigates to the tool selection page
When the page loads for the first time
Then no radio button is pre-selected
And the tool selection field is in focus-ready state
And the Continue button displays enabled state (not greyed out)
And the page heading clearly indicates: "Select the assessment tool for [Condition Name]"
```

### Scenario 3: Tool Selection - Action
**Title**: User selects a tool and selection persists

```gherkin
Given the user is on the tool selection page
When I click the radio button for "FourMats"
Then "FourMats" radio button becomes selected
And visual feedback shows the selected state: [checked indicator, highlight, etc.]
And the selection is displayed in the form state
And clicking another tool option switches the selection
And the selected tool persists when user navigates away and returns
```

### Scenario 4: Navigation
**Title**: User proceeds to assessment with selected tool

```gherkin
Given the user has selected a tool: "FourMats"
When the user clicks the Continue button
Then the user is navigated to the assessment page
And the page displays: "Conducting [Tool Name] Assessment for [Condition Name]"
And the selected tool is passed to the assessment page
And the condition selection is retained
And the user can return to this page via Back button (selections retained)
```

### Scenario 5: Validation
**Title**: User cannot proceed without selection

```gherkin
Given the user is on the tool selection page
When the user clicks Continue without selecting a tool
Then an error message displays: "Please select an assessment tool to continue"
And the error appears in red, near the radio button group
And the error message includes role="alert" for screen readers
And the user remains on the tool selection page
And focus returns to the radio button group
```

### Scenario 6: Accessibility
**Title**: WCAG 2.1 AA compliance for keyboard and screen reader users

```gherkin
Given the page is rendered
When a user navigates using Tab key
Then all interactive elements (radio buttons, Continue button) are focusable in logical order:
  1. Back button
  2. First tool radio button
  3. Second tool radio button (if multiple)
  4. Continue button
And each radio button announces via screen reader: "[Tool Name], radio button, [selected/not selected]"
And the fieldset is labeled: "Tool Selection"
And focus indicator is visible (minimum 3:1 contrast)
And colour contrast ratio is at least 4.5:1 (text vs. background)
And all images/icons have alt text describing the tool
```

---

## Definition of Done

- [x] All 6 acceptance criteria met and validated by QA
- [x] Code peer reviewed and approved
- [x] Unit tests written (80%+ coverage)
- [x] Accessibility testing completed (WCAG 2.1 AA)
- [x] UI matches Figma design (link: [design link])
- [x] Browser compatibility verified (Chrome, Firefox, Safari, Edge)
- [x] Mobile responsiveness tested (portrait/landscape at 375px, 768px, 1024px)
- [x] Documentation updated (UX patterns, accessibility note)
- [x] Ready for UAT

---

## Design Reference
[DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=0-1&p=f&t=gJFnGjUAnCoFKNFr-0)

Template: [`templates/UI-Template.md`](../templates/UI-Template.md)

Components: [`COMPONENTS.md`](../COMPONENTS.md)

Used components in this story:
- [Back Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6010-5423)
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-142)
- [Radios](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6218-4824)

[Link to the feature-specific Figma design or mockup showing the tool selection page]

## Test Plan
[Link to test cases created from these acceptance criteria]

## Notes
- Tool options may vary by condition; backend determines available tools
- Selection should be retained in component state (React state, Vue data, etc.)
- Error message should have aria-live="polite" for dynamic announcement
