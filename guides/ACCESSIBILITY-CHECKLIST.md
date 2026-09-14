# Accessibility Checklist: WCAG 2.1 Level AA

This checklist ensures all UI user stories meet **WCAG 2.1 Level AA** accessibility standards. Use this for every UI story before marking it done.

---

## Keyboard Navigation

- [ ] **Tab Key Navigation**
  - All interactive elements are reachable using Tab key
  - Tab order is logical and predictable
  - No keyboard traps (user can escape any element)
  - Recommended tab order: Back → Form fields → Submit

- [ ] **Arrow Keys (for radio buttons, dropdowns, menus)**
  - UP/DOWN arrows move between options
  - LEFT/RIGHT arrows move between options
  - Pressing an arrow key doesn't submit the form
  - Focus wraps: last item + DOWN = first item

- [ ] **Enter/Space Keys**
  - Space/Enter activates buttons and checkboxes
  - Space/Enter doesn't trigger page reload
  - Works for all clickable elements

- [ ] **Escape Key**
  - Escape closes modals and dropdowns
  - Escape cancels in-progress actions

- [ ] **Focus Management**
  - Focus visible at all times (not hidden)
  - Focus outline has minimum 3:1 contrast
  - Focus outline is at least 2px visible

---

## Screen Reader Support (NVDA, JAWS, VoiceOver)

- [ ] **Page Structure**
  - Page has exactly one `<h1>` (main heading)
  - Headings are hierarchical: `<h1>` → `<h2>` → `<h3>` (no skipping levels)
  - Headings are descriptive (not "Click Here", but "Select Assessment Tool")

- [ ] **Form Labels**
  - Every form input has associated `<label>` (not just placeholder text)
  - Labels are correctly linked to inputs using `for` attribute
  - Labels are visible (don't hide labels for sighted users)

- [ ] **Form Fields**
  - Error messages are announced: `role="alert"`
  - Required fields marked: `aria-required="true"` or `required` HTML attribute
  - Radio buttons grouped: `<fieldset>` + `<legend>`
  - Checkboxes grouped: `<fieldset>` + `<legend>`

- [ ] **Buttons**
  - Button text is descriptive: "Continue" (not "Next" or "OK")
  - Button purpose is clear from text alone
  - Icon buttons have `aria-label`: `<button aria-label="Close menu">`

- [ ] **Links**
  - Link text is descriptive: "View assessment results" (not "Click here")
  - Links announce their purpose

- [ ] **Images & Icons**
  - All images have `alt` text
  - Decorative images have `alt=""`
  - Icon buttons have `aria-label` or screen-reader-only text

- [ ] **Dynamic Content**
  - Error messages use `aria-live="polite"`
  - Success messages use `aria-live="assertive"` (high priority)
  - Loading states announced: "Loading...please wait"

- [ ] **Skip Links**
  - "Skip to main content" link visible on Tab
  - Allows screen reader users to skip navigation

---

## Color Contrast

**WCAG 2.1 AA Requirements:**
- Normal text: 4.5:1 (foreground vs. background)
- Large text (18pt+ or 14pt+ bold): 3:1
- UI components & borders: 3:1

- [ ] **Text Contrast**
  - Heading text passes 4.5:1 contrast test
  - Body text passes 4.5:1 contrast test
  - Link text passes 4.5:1 contrast (both default and hover states)
  - Button text passes 4.5:1 contrast
  - Error messages pass 4.5:1 contrast

- [ ] **Interactive Element Contrast**
  - Radio button outlines: 3:1 contrast
  - Checkbox outlines: 3:1 contrast
  - Input field borders: 3:1 contrast (unfocused)
  - Focus indicators: 3:1 contrast (or higher)

- [ ] **Color Not the Only Indicator**
  - Errors not indicated by color alone
  - Example: Error message includes icon + text (not just red text)
  - Success not indicated by color alone
  - Example: Success includes icon + text + checkmark

**Test With:**
- Chrome DevTools > Lighthouse > Accessibility
- WebAIM Contrast Checker: https://webaim.org/resources/contrastchecker/
- Colour Contrast Analyzer tool

---

## Responsive Design

- [ ] **Mobile (375px - portrait)**
  - All elements are visible (no horizontal scroll)
  - Text is readable (16px+ for body text)
  - Buttons/inputs are large enough (48px minimum touch target)
  - No pinch-to-zoom required for readability

- [ ] **Tablet (768px - portrait)**
  - Layout adapts smoothly
  - Touch targets are appropriate size

- [ ] **Desktop (1024px+)**
  - Layout optimized for larger screen
  - No huge whitespace or broken layout

---

## Motion & Animations

- [ ] **Reduced Motion**
  - Page respects `prefers-reduced-motion` setting
  - Auto-play animations can be stopped/disabled
  - No auto-playing videos with sound
  - Animations don't flash more than 3x per second (seizure safety)

```css
/* Example: Respect reduced motion setting */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Zoom & Text Resizing

- [ ] **Zoom Support**
  - Page doesn't break at 200% zoom
  - All content remains accessible at 200% zoom
  - No horizontal scroll required at 200% zoom
  - Test: Browser > Zoom > 200%

- [ ] **Text Resizing**
  - Increase text size via browser: Ctrl/Cmd + [ + ]
  - Layout doesn't break
  - Text doesn't overlap with other content
  - Don't use `font-size: 12px` (use relative units: `1rem`)

---

## Language & Readability

- [ ] **Page Language**
  - HTML has `lang` attribute: `<html lang="en">`
  - Language changes are marked: `<span lang="cy">Cymraeg text</span>`

- [ ] **Readability**
  - Sentences are clear and concise
  - Medical/technical terms are explained
  - Reading level appropriate for audience
  - No walls of text (break into short paragraphs)

- [ ] **Instructions**
  - Instructions are clear and provided in text (not just video/audio)
  - Steps are numbered or clearly separated

---

## Forms & Errors

- [ ] **Error Messages**
  - Error text is specific: "Month field is required" (not "Invalid input")
  - Errors are near the field they relate to
  - Errors are linked to field: `aria-describedby="error-message"`
  - Errors have icon + color + text (not color alone)

- [ ] **Form Assistance**
  - Help text is provided if needed
  - Required fields are marked
  - Field formats are explained: "Enter date as MM/DD/YYYY"

- [ ] **Auto-Submit Prevention**
  - Changing a selection doesn't auto-submit form
  - User must click Submit button
  - Auto-save features announce their status

---

## Time-Based Content

- [ ] **Time Limits**
  - Sessions don't time out without warning
  - If timeout occurs, user can extend
  - No audio plays automatically (except in video players)
  - Animations don't auto-start

---

## Video & Audio

- [ ] **Captions**
  - All videos have captions (text of spoken content)
  - Captions include speaker identification
  - Sound cues are captioned: "[alert sound]"

- [ ] **Audio Descriptions**
  - Important visual information has audio description
  - OR transcript provided

- [ ] **Controls**
  - Play/pause button clearly available
  - Volume control available
  - Captions can be toggled on/off

---

## Links & Navigation

- [ ] **Link Text**
  - Links are descriptive: "View patient history" (not "Click here")
  - Link purpose is clear from text alone
  - Links that open in new window/tab announce it: "Opens in new window"

- [ ] **Navigation**
  - Navigation menu is keyboard accessible
  - Current page is indicated in menu
  - Breadcrumbs help user understand location

---

## Tables (if applicable)

- [ ] **Table Structure**
  - Table has `<caption>` describing contents
  - Header cells use `<th>` (not `<td>`)
  - Data cells use proper associations: `scope="row"` or `scope="col"`
  - Complex tables have `summary` or description

---

## Testing Checklist

**Automated Tools (First Pass):**
- [ ] Run Lighthouse (Chrome DevTools)
- [ ] Run WAVE (WebAIM browser extension)
- [ ] Run Axe DevTools
- [ ] Check contrast with WebAIM Contrast Checker

**Manual Testing (Required):**
- [ ] Keyboard-only navigation (no mouse)
- [ ] Screen reader testing:
  - [ ] NVDA (Windows)
  - [ ] JAWS (Windows)
  - [ ] VoiceOver (macOS)
- [ ] Zoom to 200% (browser zoom, not pinch-zoom)
- [ ] Color contrast verification
- [ ] Mobile testing (375px)
- [ ] Motion/animation testing with `prefers-reduced-motion`

**Browser Testing:**
- [ ] Chrome latest
- [ ] Firefox latest
- [ ] Safari latest
- [ ] Edge latest

---

## WCAG 2.1 Level AA Success Criteria Covered

| Criterion | Focus Area | Tested |
|-----------|-----------|--------|
| 1.1.1 Non-text Content | Images, icons, alt text | ✓ |
| 1.4.3 Contrast (Minimum) | Color contrast 4.5:1 | ✓ |
| 1.4.10 Reflow | Mobile zoom, responsive | ✓ |
| 1.4.11 Non-text Contrast | UI component contrast 3:1 | ✓ |
| 2.1.1 Keyboard | Tab, arrow keys, enter | ✓ |
| 2.1.2 No Keyboard Trap | Can escape any element | ✓ |
| 2.4.3 Focus Order | Logical, visible focus | ✓ |
| 2.4.7 Focus Visible | Focus indicator visible | ✓ |
| 3.2.1 On Focus | No unexpected context changes | ✓ |
| 3.3.1 Error Identification | Error messages | ✓ |
| 3.3.3 Error Suggestion | Help text provided | ✓ |
| 4.1.2 Name, Role, Value | Form labels, buttons | ✓ |
| 4.1.3 Status Messages | aria-live for alerts | ✓ |

---

## Sign-Off

When accessibility is complete:

```
✅ Accessibility Testing Completed
- Automated tools: PASS
- Keyboard navigation: PASS
- Screen reader (NVDA): PASS
- Color contrast: PASS
- Mobile (375px): PASS
- Zoom (200%): PASS
- Signed off by: [QA Accessibility Lead]
- Date: [Date]
- WCAG 2.1 Level AA: COMPLIANT
```

---

**Resources:**

- [WCAG 2.1 Standard](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [NVDA Screen Reader (free)](https://www.nvaccess.org/)
- [WAVE Accessibility Tool](https://wave.webaim.org/)
- [Axe DevTools](https://www.deque.com/axe/devtools/)

---

**Remember: Accessibility is not an afterthought. Include it from the start of design and development!**
