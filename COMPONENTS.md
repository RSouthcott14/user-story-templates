# DHCW Design System Components Reference

**Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=0-1&p=f&t=gJFnGjUAnCoFKNFr-0)

This file documents all available components in the DHCW Design System. **Use this guide when creating user stories to ensure correct component usage.**

---

## 📋 How to Use This Guide

When writing acceptance criteria for UI stories:
1. Reference the appropriate component from this guide
2. Specify component variants (size, state, type)
3. Include accessibility requirements
4. Link to Figma component in story details

**Component Reference Format:**
- **Component Name** — What it does
- **Variants** — Available sizes/states
- **Usage** — When to use (and when NOT to use)
- **Accessibility** — WCAG 2.1 AA requirements
- **Figma Link** — Direct link to component in design system

---

## 🎨 Core Components

### Buttons

#### Primary Button
- **What it does:** Call-to-action button for primary user actions
- **Variants:** Small, Medium (default), Large | Enabled, Hover, Active, Disabled, Loading
- **Usage:** 
  - ✅ Submit forms, confirm actions, proceed to next step
  - ❌ Do NOT use for secondary actions (use Secondary Button)
  - ❌ Do NOT use for navigation (use Links)
- **Accessibility:**
  - Minimum 44px height and 44px width (touch targets)
  - Clear focus indicator visible
  - Sufficient color contrast (WCAG AA minimum)
  - Text: "Click to [action]" or equivalent aria-label
- **Figma:** [Primary Button Component]

#### Secondary Button
- **What it does:** Secondary action button for supporting user flows
- **Variants:** Small, Medium (default), Large | Enabled, Hover, Active, Disabled, Loading
- **Usage:**
  - ✅ Cancel, go back, or alternative actions
  - ✅ Multiple actions on same page
  - ❌ Do NOT use for primary action (use Primary Button)
- **Accessibility:**
  - Minimum 44px height and 44px width
  - Clear focus indicator
  - Sufficient color contrast
- **Figma:** [Secondary Button Component]

#### Button Group
- **What it does:** Container for multiple related buttons
- **Variants:** Horizontal (default), Vertical | 2-4 buttons
- **Usage:**
  - ✅ Yes/No dialogs
  - ✅ Multiple action choices
  - ❌ Do NOT mix button types in group (all Primary or all Secondary)
- **Accessibility:**
  - Tab order preserved left-to-right
  - Each button independently focusable
  - Group labeled with aria-group if needed
- **Figma:** [Button Group Component]

---

### Forms

#### Input Field (Text)
- **What it does:** Single-line text input for user data entry
- **Variants:** Text, Email, Password, URL, Number | Default, Focus, Error, Disabled, Filled
- **Usage:**
  - ✅ Email addresses, usernames, single-line text
  - ❌ Do NOT use for multi-line text (use Text Area)
  - ❌ Do NOT use for selections (use Dropdown, Radio)
- **Accessibility:**
  - Associated label (for/id)
  - Clear focus indicator
  - Error messages linked via aria-describedby
  - Placeholder ≠ label
- **Figma:** [Input Field Component]

#### Text Area
- **What it does:** Multi-line text input for longer content
- **Variants:** Small, Medium (default), Large | Default, Focus, Error, Disabled, Filled
- **Usage:**
  - ✅ Comments, descriptions, notes
  - ✅ Text longer than one line
  - ❌ Do NOT use for single-line input (use Input Field)
- **Accessibility:**
  - Associated label (for/id)
  - Clear focus indicator
  - Resize handle visible (if resizable)
  - Character count if max length exists
- **Figma:** [Text Area Component]

#### Radio Button Group
- **What it does:** Select one option from a mutually exclusive list
- **Variants:** Vertical (default), Horizontal | 2+ options
- **Usage:**
  - ✅ Selecting ONE option (mutually exclusive)
  - ✅ 3-5 options visible
  - ❌ Do NOT use for multiple selections (use Checkboxes)
  - ❌ Do NOT use for 6+ options (use Dropdown)
- **Accessibility:**
  - Fieldset + legend for group
  - Labels clickable (not just radio button)
  - Keyboard navigation: Arrow keys to select
  - Clear focus indicator on selected option
- **Figma:** [Radio Button Component]

#### Checkbox Group
- **What it does:** Select multiple options from a list
- **Variants:** Vertical (default), Horizontal | 2+ options
- **Usage:**
  - ✅ Multiple selections allowed
  - ✅ "Select all" patterns
  - ❌ Do NOT use for single selection (use Radio Button)
- **Accessibility:**
  - Fieldset + legend for group
  - Labels clickable
  - Keyboard navigation: Space to toggle, Tab to move between
  - Indeterminate state shown clearly
- **Figma:** [Checkbox Component]

#### Dropdown / Select
- **What it does:** Collapsed list of selectable options
- **Variants:** Single select (default), Multi-select | Default, Open, Error, Disabled
- **Usage:**
  - ✅ 5+ options
  - ✅ Space-constrained layouts
  - ❌ Do NOT use for 3-4 options (use Radio Buttons)
  - ❌ Do NOT use if all options should be visible (use Radio/Checkboxes)
- **Accessibility:**
  - Associated label (for/id)
  - Keyboard: Arrow keys to navigate, Enter to select
  - Screen reader announces option count
  - Focus indicator visible on open menu
- **Figma:** [Dropdown Component]

#### Form Section / Fieldset
- **What it does:** Logical grouping of related form fields
- **Variants:** Standard, Card, Collapsible
- **Usage:**
  - ✅ Group related inputs (e.g., Address fields)
  - ✅ Multi-step forms
  - ✅ Optional vs. Required fields
- **Accessibility:**
  - Fieldset + legend required
  - Clear visual separation from other sections
  - Required field indicator if applicable
- **Figma:** [Form Section Component]

---

### Navigation

#### Tab Group
- **What it does:** Switch between content panels using tab navigation
- **Variants:** Horizontal (default), Vertical | Standard, Icon+Label
- **Usage:**
  - ✅ Multiple related content sections
  - ✅ Dashboard pages with tabs
  - ❌ Do NOT use for main page navigation (use Navigation Bar)
  - ❌ Do NOT use for multi-step flow (use Wizard/Stepper)
- **Accessibility:**
  - ARIA role="tablist" on container
  - ARIA role="tab" on tab buttons
  - ARIA role="tabpanel" on content sections
  - Keyboard: Arrow keys between tabs, Enter/Space to activate
  - Active tab indicator clear and persistent
- **Figma:** [Tab Group Component]

#### Navigation Bar
- **What it does:** Primary navigation across application
- **Variants:** Horizontal (top), Vertical (sidebar) | Logo + Menu items
- **Usage:**
  - ✅ Main app navigation
  - ✅ Persistent across pages
  - ✅ 4-8 main sections
- **Accessibility:**
  - Semantic nav element or role="navigation"
  - Current page highlighted clearly
  - Menu items as links with href
  - Mobile: Hamburger menu with proper labeling
  - Skip navigation link at top
- **Figma:** [Navigation Bar Component]

#### Breadcrumb
- **What it does:** Show current location in hierarchy
- **Variants:** Text, Text+Icon | With/without home icon
- **Usage:**
  - ✅ Deep page hierarchies
  - ✅ Multi-level navigation
  - ❌ Do NOT use on single-level pages
- **Accessibility:**
  - Semantic nav or role="navigation"
  - Use "/" or ">" as separators (aria-label)
  - Final item is aria-current="page"
  - Links must have href
- **Figma:** [Breadcrumb Component]

#### Pagination
- **What it does:** Navigate between pages of content
- **Variants:** Number buttons, Previous/Next, Jump to page
- **Usage:**
  - ✅ Lists with 20+ items
  - ✅ Search results
  - ❌ Do NOT use for 1-2 pages (just show all content)
- **Accessibility:**
  - Current page marked with aria-current="page"
  - Disabled state for first/last page
  - Keyboard navigation
  - Screen reader announces "Page X of Y"
- **Figma:** [Pagination Component]

---

### Feedback & Information

#### Alert / Banner
- **What it does:** Display important messages to user
- **Variants:** Success, Warning, Error, Info | Dismissible, With icon, Full-width
- **Usage:**
  - ✅ System messages (success, errors, warnings)
  - ✅ Important announcements
  - ✅ Form validation feedback
- **Accessibility:**
  - role="alert" for important messages
  - High contrast colors (WCAG AAA if possible)
  - Icon + text (not icon-only)
  - If dismissible: Visible close button, keyboard accessible
- **Figma:** [Alert Component]

#### Tooltip
- **What it does:** Brief help text on hover/focus
- **Variants:** Top, Bottom, Left, Right | Light/Dark background
- **Usage:**
  - ✅ Brief helper text (max 2 sentences)
  - ✅ Icon explanations
  - ❌ Do NOT use for critical info (use Help text inline)
  - ❌ Do NOT use on mobile (hover doesn't exist)
- **Accessibility:**
  - Show on focus + hover
  - Keyboard accessible
  - Not blocking content
  - aria-label or aria-describedby
- **Figma:** [Tooltip Component]

#### Loading Indicator / Spinner
- **What it does:** Show that content is loading
- **Variants:** Spinner, Progress bar, Skeleton loader
- **Usage:**
  - ✅ Async operations (API calls, file uploads)
  - ✅ Page transitions
  - ✅ Content rendering
- **Accessibility:**
  - aria-label="Loading..." or similar
  - aria-live="polite" for dynamic regions
  - Don't disable inputs while loading (unless necessary)
  - Cancel/Stop button if applicable
- **Figma:** [Loading Indicator Component]

---

### Cards & Containers

#### Card
- **What it does:** Contained unit of related content
- **Variants:** Basic, With image, With actions (buttons)
- **Usage:**
  - ✅ Grid layouts (multiple items)
  - ✅ Featured content
  - ✅ Modular content blocks
- **Accessibility:**
  - Semantic heading hierarchy
  - If clickable: Entire card clickable (not just link)
  - Focus indicator visible around entire card
  - aria-label for context if needed
- **Figma:** [Card Component]

#### Modal / Dialog
- **What it does:** Overlay requiring user action or confirmation
- **Variants:** Alert, Confirmation, Form, Custom
- **Usage:**
  - ✅ Confirm destructive actions (delete, confirm)
  - ✅ Focused data entry (forms in modal)
  - ✅ Required user decisions
  - ❌ Do NOT use for optional messages (use Alert)
  - ❌ Do NOT use for page-level navigation
- **Accessibility:**
  - role="dialog" or role="alertdialog"
  - aria-modal="true"
  - Focus management: trap focus inside modal
  - Escape key closes modal
  - aria-labelledby for title, aria-describedby for content
  - Close button + title clearly labeled
- **Figma:** [Modal Component]

#### Drawer / Sidebar Panel
- **What it does:** Slide-out panel from edge of screen
- **Variants:** Left, Right, Full-height | Overlay, Inline
- **Usage:**
  - ✅ Secondary navigation
  - ✅ Filters, options panels
  - ✅ Mobile navigation
- **Accessibility:**
  - Focus management (trap in drawer when open)
  - Escape key closes
  - Swipe support on touch devices
  - aria-hidden="true" on main content when open
- **Figma:** [Drawer Component]

---

### Lists & Tables

#### List (Unordered, Ordered)
- **What it does:** Display items in bulleted or numbered list
- **Variants:** Bullet list, Numbered list, Icon list
- **Usage:**
  - ✅ Instructions, features, requirements
  - ✅ Simple content grouping
  - ❌ Do NOT use for tabular data (use Table)
  - ❌ Do NOT use for navigation (use Nav component)
- **Accessibility:**
  - Semantic ul/ol/li elements
  - Proper nesting for sub-lists
  - Screen reader announces item count
  - Links within lists properly marked
- **Figma:** [List Component]

#### Table
- **What it does:** Display structured tabular data
- **Variants:** Basic, Sortable, Selectable, Expandable rows
- **Usage:**
  - ✅ Structured data comparison
  - ✅ Lists where columns matter
  - ✅ Sortable/filterable data
- **Accessibility:**
  - Semantic table/thead/tbody/tr/th/td elements
  - Table headers: scope="col" or scope="row"
  - Table caption or aria-label
  - Sortable columns: announce sort order
  - Keyboard navigation (Tab, Arrow keys)
  - Responsive: Stack on mobile (not horizontal scroll)
- **Figma:** [Table Component]

---

### Typography

#### Heading Levels (H1, H2, H3, H4, H5, H6)
- **What it does:** Semantic page structure and hierarchy
- **Variants:** H1, H2, H3, H4, H5, H6 | Display, Large, Medium, Small
- **Usage:**
  - ✅ Page titles (H1, single per page)
  - ✅ Section headings (H2, H3, H4 in order)
  - ❌ Do NOT skip heading levels (e.g., H1 to H3)
  - ❌ Do NOT use for styling only (use CSS classes)
- **Accessibility:**
  - Semantic heading elements (not div with heading style)
  - Sequential heading hierarchy
  - Screen reader properly announces heading level
- **Figma:** [Heading Components]

#### Paragraph / Body Text
- **What it does:** Regular body content text
- **Variants:** Body Large, Body Medium (default), Body Small | Regular, Bold, Italic
- **Usage:**
  - ✅ Primary content paragraphs
  - ✅ Form labels, help text
  - ✅ Descriptions
- **Accessibility:**
  - Line height ≥ 1.5 for readability
  - Color contrast ≥ 4.5:1 (normal text), ≥ 3:1 (large text)
  - Font size ≥ 12px minimum
  - Not all caps (reduces readability)
- **Figma:** [Body Text Component]

#### Label
- **What it does:** Describe form fields and inputs
- **Variants:** Standard, Required, Optional, Disabled
- **Usage:**
  - ✅ Every form field MUST have visible label
  - ✅ Required/optional indicators
  - ❌ Do NOT use placeholder as label
- **Accessibility:**
  - Associated with input via for/id
  - Always visible (not hidden)
  - Clear and descriptive text
  - Required indicator (e.g., asterisk with aria-label)
- **Figma:** [Label Component]

---

## ✅ Component Validation Checklist

When reviewing acceptance criteria for UI stories, verify:

- [ ] Components used match those in this guide
- [ ] Component variants are available in Figma
- [ ] Accessibility requirements are included in scenarios
- [ ] All form fields have associated labels
- [ ] Button types are used correctly (Primary vs Secondary)
- [ ] Dialog/Modal uses proper ARIA attributes
- [ ] Responsive behavior specified (mobile, tablet, desktop)
- [ ] Color contrast meets WCAG AA minimum
- [ ] Keyboard navigation specified
- [ ] Focus indicators mentioned
- [ ] Loading states defined
- [ ] Error states with messages defined

---

## 🔍 Component Selection Decision Tree

Use this to select the right component:

### "I need a clickable element..."
- Primary action? → **Primary Button**
- Secondary action? → **Secondary Button**
- Navigation? → **Link** (styled or in Nav component)
- Toggle on/off? → **Toggle / Checkbox**

### "I need to collect user input..."
- Text input? → **Input Field** or **Text Area**
- Single selection? → **Radio Button Group**
- Multiple selections? → **Checkbox Group**
- Many options? → **Dropdown / Select**
- Date/time? → **Date Picker** / **Time Picker**
- File upload? → **File Upload** component

### "I need to show multiple content sections..."
- Multiple pages of same content? → **Pagination**
- Switchable content tabs? → **Tab Group**
- Hidden/collapsible content? → **Accordion** or **Drawer**
- Breadcrumb trail? → **Breadcrumb**

### "I need to communicate something to user..."
- Critical message requiring action? → **Alert** (role="alert")
- Help/hint text? → **Tooltip** or **Help Text**
- Loading in progress? → **Loading Indicator**
- Confirm destructive action? → **Modal (Confirmation)**

### "I need to organize content..."
- List of items? → **Card Grid** or **List**
- Data comparison? → **Table**
- Grouped form inputs? → **Form Section / Fieldset**
- Featured content? → **Card**

---

## 📞 Questions?

- **Component not listed?** Add it to this guide or ask your design team
- **Unsure which to use?** Refer to the Decision Tree above or check Figma design system
- **Accessibility questions?** See ACCESSIBILITY-CHECKLIST.md in this repo

---

## 🔄 Keeping This Guide Updated

When design system changes:
1. Update this file
2. Commit: `git add COMPONENTS.md && git commit -m "Update: Component changes from Figma"`
3. Push: `git push`
4. Notify team of updates

---

**Last Updated:** [Today's Date]  
**Figma Design System:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=0-1&p=f&t=gJFnGjUAnCoFKNFr-0)
