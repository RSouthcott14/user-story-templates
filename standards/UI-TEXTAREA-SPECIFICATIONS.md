# UI Textarea Specifications

**Platform:** Choose Pharmacy NextGen  
**Version:** 1.0  
**Applies To:** All UI user stories featuring textarea/multi-line text input components

---

## Overview

All textarea components in the Choose Pharmacy NextGen platform MUST follow standardized specifications for character limits, dynamic character counting, layout, and user interaction patterns. These specifications ensure consistency, usability, and accessibility across all consultation journeys and forms.

---

## Character Limit & Counter Requirements

### Character Limit
- **Default limit:** 2000 characters (unless explicitly specified differently in a story)
- Character limit is a hard constraint—users cannot exceed the limit
- Character counter must always be visible and update in real-time

### Character Counter Display

**Initial State:**
When the page loads or the textarea is empty, the character counter displays:
```
"You have 2000 characters remaining"
```

**Dynamic Updates:**
As the user types or deletes text, the counter updates in real-time to reflect remaining characters:
```
"You have [2000 - n] characters remaining"
```

Where `n` = number of characters currently entered.

**Examples:**
- Empty textarea: `"You have 2000 characters remaining"`
- After entering 150 characters: `"You have 1850 characters remaining"`
- After entering 500 characters: `"You have 1500 characters remaining"`
- At 2000 characters: `"You have 0 characters remaining"`

### Counter Position & Styling
- Character counter text displays **below** the textarea
- Counter uses a standard color (typically gray, e.g., #666666)
- Counter text is smaller than body text (e.g., 12px or 0.875rem)
- Counter updates without page refresh or delay (real-time)

---

## Textarea Layout & Sizing

### Width
- Textarea shall utilize the **maximum available section width**
- Textarea expands to fill its container (responsive to viewport width)
- Textarea width is constrained only by section padding/margins

### Margins
- **Equal margins** shall be maintained on the **left and right sides** of the textarea within its section
- Left margin = Right margin (horizontally symmetric)
- Vertical margins (top/bottom) follow DHCW Design System V2 spacing standards

### Field Height
- **Initial height:** Minimum 3-4 lines of text (suggested 100–120px)
- **Expandable:** User can resize/expand the textarea by:
  - Dragging the resize handle (if provided)
  - Browser default resize handle (bottom-right corner)
  - Double-clicking to maximize (if supported by design)

### Scrolling
- When text exceeds the visible textarea height AND the field is **not expanded**, a vertical scrollbar appears
- User can scroll within the textarea to view and edit long text without expanding
- Scrolling does not affect the character counter position (counter remains fixed below textarea)

---

## User Interaction Patterns

### Typing & Text Entry
- User can type continuously up to 2000 characters
- At 2000 characters, further input is blocked (no additional characters can be added)
- Character counter updates with each keystroke (additions and deletions)
- Paste operations respect the 2000-character limit; if pasted text would exceed the limit, it is truncated or rejected

### Field Expansion
- User can manually expand/resize the textarea using:
  - Browser's built-in resize handle (if CSS `resize: both` or `resize: vertical`)
  - Custom expand button (if provided in design)
- Expanded state is temporary for the current session; it does not persist after page reload (unless explicitly saved in state)
- When expanded, the character counter remains visible and continues to update

### Clearing Text
- User can select all text (Ctrl+A / Cmd+A) and delete
- Character counter immediately updates to reflect the cleared state: `"You have 2000 characters remaining"`

---

## Validation & Error Handling

### Character Limit Exceeded (Pre-Validation)
- User cannot type or paste beyond 2000 characters (prevented at input level)
- No error message is displayed; the field simply stops accepting input
- Character counter will never display a negative or "exceeded" state

### Required Textarea Field Validation
If a textarea is marked as **mandatory**:

```gherkin
Given the user is on a form with a required textarea
When the user attempts to submit without entering text in the required textarea
Then an error message displays: "Enter *{fieldName}*"
And the error appears near the textarea field
And the error is styled with: red text / warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the user remains on the current page
And no data is persisted
```

**Example error messages:**
- `"Enter advice to patient"` (for "Advice to patient" textarea)
- `"Enter reason"` (for "Reason not given" textarea)
- `"Enter notes"` (for "POCT notes" textarea)

### Optional Textarea Field
- If a textarea is **optional** (not marked with asterisk *), the user may leave it empty
- No validation error is displayed if an optional textarea is empty

---

## Acceptance Criteria Requirements

All UI stories featuring a textarea MUST include the following in their Acceptance Criteria:

### Page Structure & Content
In **Scenario 1 (Page Structure & Content)**, include:
```gherkin
And the textarea field is displayed with:
  - Label: "[Field Name]" [with required indicator (*) if mandatory]
  - Placeholder text: "[Guidance text, e.g., 'Enter your advice']"
  - Character counter below the field displaying: "You have 2000 characters remaining"
  - Resize capability: User can expand the textarea to view/edit long text
And the textarea utilizes the maximum available section width
And equal margins are maintained on the left and right sides within the section
```

### Default State
In **Scenario 2 (Default State)**, include:
```gherkin
Given the user navigates to the page with the textarea
When the page loads for the first time
Then the textarea is empty
And the character counter displays: "You have 2000 characters remaining"
And the textarea is [enabled/disabled] [specify based on preconditions]
And the textarea is not expanded (default height: minimum 3-4 lines)
```

### User Interaction
If a textarea has content entry, include a scenario covering:
```gherkin
Given the user is on the page with the textarea
When the user types text: "[Sample text]"
Then the text is displayed in the textarea
And the character counter updates dynamically to: "You have [remaining] characters remaining"
And the user can continue typing until 2000 characters are reached
And when 2000 characters are entered, no further input is accepted
And the user can expand the textarea by dragging the resize handle or double-clicking
And when expanded, the character counter remains visible and continues to update
And the scroll function is available for text exceeding the textarea height (unexpanded)
```

### Validation (if applicable)
If a textarea is **required**, include:
```gherkin
Given the user is on the form with the required textarea
When the user [attempts to submit/navigate away] without entering text
Then an error message displays: "Enter *{fieldName}*"
And the error appears [near the textarea / in error summary]
And the error is styled in red with a warning icon
And the error message has role="alert" for screen reader announcement
And aria-live="polite"
And the user remains on the current page
And no data is persisted
```

---

## Accessibility Requirements

All textareas MUST meet WCAG 2.1 Level AA standards:

### Screen Reader Support
- Textarea has an associated `<label>` element (not placeholder text alone)
- Label is correctly linked using `for` attribute to textarea `id`
- Label text is announced by screen readers when field is focused
- Required field is marked: `aria-required="true"` or HTML `required` attribute
- Character counter is announced: `aria-live="polite"` and `role="status"`
- Error messages use `role="alert"` with `aria-live="assertive"`

### Keyboard Navigation
- Tab key enters the textarea from previous element
- Text entry and editing work normally (arrow keys, delete, backspace, etc.)
- Shift+Tab exits the textarea to the previous element
- Ctrl+A / Cmd+A selects all text
- Ctrl+X / Cmd+X cuts text
- Ctrl+C / Cmd+C copies text
- Ctrl+V / Cmd+V pastes text (respecting 2000-character limit)
- No keyboard trap; user can easily exit the textarea

### Color Contrast
- Textarea border: 3:1 contrast (unfocused) / 4.5:1 contrast (focused)
- Text inside textarea: 4.5:1 contrast
- Character counter text: 4.5:1 contrast
- Error message text: 4.5:1 contrast
- Focus indicator: at least 3:1 contrast, minimum 2px visible outline

### Focus Management
- Textarea has a visible focus indicator (e.g., 2–3px border or outline)
- Focus indicator remains visible at all times
- Error-related focus moves to the textarea or error summary (based on design)

---

## Example Acceptance Criteria Block

When a story includes a textarea, the acceptance criteria should include sections similar to this:

```gherkin
### Scenario 1: Textarea displays with character counter
Given the user navigates to the page
When the page loads
Then the textarea field is displayed with:
  - Label: "Advice to patient (optional)"
  - Placeholder: "Enter any relevant advice for the patient"
  - Character counter: "You have 2000 characters remaining"
And the textarea utilizes the maximum available section width
And equal margins are maintained on left and right sides
And the textarea has a default height of 3–4 lines
And the resize handle is available at the bottom-right corner

### Scenario 2: Character counter updates dynamically
Given the user is on the page with the textarea
When the user types "This is important advice."
Then the text is displayed in the textarea
And the character counter updates to: "You have 1977 characters remaining"
And when the user continues typing to 500 characters, the counter shows: "You have 1500 characters remaining"
And when the user deletes text, the counter increases accordingly
And at 2000 characters, no further input is accepted

### Scenario 3: User expands textarea for long text
Given the textarea contains several lines of text
When the user drags the resize handle downward
Then the textarea expands vertically
And the character counter remains visible below
And the user can continue typing and editing in the expanded state

### Scenario 4: Scrolling within collapsed textarea
Given the textarea contains long text (>4 lines) but is not expanded
When the user scrolls within the textarea
Then the text scrolls to reveal hidden lines
And the character counter remains fixed below the textarea
And the resize handle remains accessible
```

---

## Related Standards

- [standards/UI-ERROR-MESSAGING.md](./UI-ERROR-MESSAGING.md) — Error message format and templates
- [COMPONENTS.md](../COMPONENTS.md) — Textarea component reference (DHCW Design System V2)
- [guides/ACCESSIBILITY-CHECKLIST.md](../guides/ACCESSIBILITY-CHECKLIST.md) — WCAG 2.1 AA compliance requirements
