# Messages Screen

## Story Type: UI

---

## Description

### Story Card
**As a** pharmacy user  
**I want** to view and manage messages from system administrators and alerts in one unified inbox  
**So that** I can quickly access important system updates and respond to critical clinical alerts without switching screens

### Context
The Messages screen is a central hub for critical communications within the Choose Pharmacy system. Pharmacy staff receive two main types of messages requiring different handling:
- **System Administrator Messages:** Important updates, maintenance notifications, and policy changes
- **Alert Messages:** Time-sensitive warnings about medication safety, drug interactions, or prescription issues

This screen integrates the Messages tab shown in the Home dashboard, providing clear visual distinction between routine system messages and high-priority clinical alerts.

### Out of Scope
- Patient messages and consultation follow-ups (future phase)
- Referral management and responses (future phase)
- Real-time message push notifications (handled by notification service)
- Message composition/reply interface (handled in separate story)
- Message search and advanced filtering (future enhancement)
- Message archiving and permanent deletion (handled separately)
- Integration with external messaging platforms

### Related Stories
- Home Dashboard — relates to: This screen is accessed via the Messages tab on the Home page

### Dependent Stories
- Message Composition Screen — blocks: Users need a compose interface to reply to messages viewed here

---

### Design Reference
**Design system:** [DHCW Design System V2](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2)

**Components used:**
- [Cards](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6082-938) — Message containers
- [Notification Banners](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6610-4602) — Alert message styling
- [Buttons](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=4008-475) — Action buttons
- [Pagination](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9568) — Navigate through message list
- [Panel](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6057-4590) — Sidebar filters
- [Back Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2?node-id=6247-9492) — Back to Home navigation

---

## Acceptance Criteria

**Total Scenarios: 8**

### Page Structure & Content

**Scenario 1: Display messages inbox with multiple message types on initial load**

```gherkin
Given the user navigates to the Messages screen
When the page loads
Then the following elements are displayed:
  - Page heading: "Messages"
  - Unread message counter badge (e.g., "3 unread")
  - Filter panel on the left sidebar with options: All, System, Alerts
  - Message list area showing messages as individual cards
  - Each message card displays:
    - Message type icon (color-coded by type)
    - Sender information (System Administrator or Alert source)
    - Message subject/preview text
    - Timestamp (e.g., "2 hours ago")
    - Unread indicator (blue dot or highlight)
And the "All" filter is selected by default
And the page shows a maximum of 10 messages per page
And pagination controls are displayed at the bottom if there are more than 10 messages
```

### Default State

**Scenario 2: Display inbox with system, patient, alert, and referral messages in default view**

```gherkin
Given the user is on the Messages screen
When the page loads for the first time
Then the message list displays:
  - System Administrator Message (blue badge/icon): "System Maintenance Scheduled"
  - Alert Message (red badge/icon): "Drug interaction warning for patient consultation"
And unread messages appear with a highlighted background
And read messages appear in standard styling
And messages are sorted by date received (most recent first)
```

**Scenario 3: Display empty state when no messages exist**

```gherkin
Given the user is on the Messages screen
When there are no messages in the inbox
Then the following empty state is displayed:
  - Icon: Envelope or inbox icon
  - Heading: "No messages yet"
  - Message: "You don't have any messages. Check back later."
And no pagination controls are displayed
```

### User Interaction

**Scenario 4: User clicks on a message card to view full message details**

```gherkin
Given the user is on the Messages screen
And the message list is displayed with multiple messages
When the user clicks on a message card
Then the message card expands or navigates to a detail view showing:
  - Full message content/body
  - Sender information (name, role, organization)
  - Timestamp and message type indicator
  - Action buttons: "Reply", "Archive", "Mark as Spam" (if applicable)
And the message is automatically marked as read
And the unread indicator is removed from that message
And the counter badge updates to reflect the new unread count
```

**Scenario 5: User filters messages by type using the sidebar filter panel**

```gherkin
Given the user is on the Messages screen
And the filter panel displays all filter options
When the user clicks on the "Alerts" filter
Then the message list is updated to show only alert messages
And the "Alerts" filter option is highlighted/selected
And the page heading updates to show "Alerts" or includes filter context
And the message count reflects only alert messages
And the filter selection persists if the user navigates away and returns
```

**Scenario 6: User marks a message as unread to return to it later**

```gherkin
Given the user is viewing a message detail
When the user clicks a "Mark as Unread" button or link
Then the message card returns to the inbox with an unread indicator
And the unread counter badge increases by one
And a confirmation message appears: "Message marked as unread"
And the message remains in the current filter view
```

### Navigation

**Scenario 7: User navigates back to home and returns to messages with state preserved**

```gherkin
Given the user is viewing the Messages screen
And has filtered messages by "Patients"
And has opened and read several messages
When the user clicks the back link or navigates to Home
And then clicks the "Messages" tab again
Then the user returns to the Messages screen
And the "Patients" filter is still selected
And the message list reflects the same filtered state
And unread/read status is preserved
And the scroll position is restored (if applicable)
```

**Scenario 8: User navigates to message reply screen from a message detail view**

```gherkin
Given the user is viewing a specific message detail
And the message is from a patient or requires a response
When the user clicks the "Reply" button
Then the user is navigated to the Message Compose screen
And the reply is pre-populated with:
  - Recipient: [Sender name]
  - Subject: "Re: [Original message subject]"
  - Quote of original message (if applicable)
And the user's previous replies or related messages are visible (if applicable)
```

### Validation

**Scenario 9: Display validation error when message fails to load**

```gherkin
Given the user is on the Messages screen
When a message fails to load due to a system error
Then an error message displays: "Unable to load message. Please try again later."
And the error is displayed as an inline notification within the message card
And the error message has role="alert" for screen reader announcement
And aria-live="polite" for dynamic content
And the user can attempt to reload by clicking a "Retry" button
And the message remains in the list but in a disabled state
And no data is persisted from the failed load
```

**Scenario 10: Display validation error when applying a filter**

```gherkin
Given the user is on the Messages screen
When the user applies a filter and the system cannot retrieve filtered messages
Then an error message displays: "Unable to apply filter. Please try again."
And the error is displayed at the top of the message list
And the message list reverts to the previously successful filter state
And the user can retry by clicking the same filter option again
And the role="alert" and aria-live="polite" attributes are applied
And no data is persisted
```

### Accessibility

**Scenario 11: Ensure keyboard navigation and screen reader compatibility**

```gherkin
Given the page is rendered with the Messages screen
When a user navigates using a keyboard
Then all interactive elements are focusable and accessible:
  - Filter buttons in sidebar
  - Message cards in the list
  - Action buttons (Reply, Archive, Mark as Unread)
  - Pagination controls
And the tab order is logical:
  1. Back link or navigation
  2. Filter buttons (top to bottom)
  3. Message cards (top to bottom)
  4. Pagination controls
And screen reader announces:
  - Message type and sender for each card: "System message from System Administrator"
  - Unread status: "Unread, 2 new messages from patients"
  - Filter status: "Alerts filter selected, showing 3 messages"
  - Button purpose: "Reply button, opens compose screen"
And colour contrast ratio is at least 4.5:1 for all text
And message type icons include alt text or aria-label:
  - System icon: "System Administrator message"
  - Patient icon: "Patient message"
  - Alert icon: "Alert message, requires attention"
  - Referral icon: "Referral message from healthcare provider"
And focus indicators are clearly visible (minimum 2px highlight)
```

---

## Message Type Specifications

### System Administrator Messages
- **Icon/Badge:** Blue circle with gear or admin icon
- **Styling:** Standard card with subtle background
- **Priority:** Medium
- **Examples:** Maintenance notifications, policy updates, system announcements

### Alert Messages
- **Icon/Badge:** Red circle with exclamation mark or warning icon
- **Border/Accent:** Prominent red left border (4px) - more prominent than others
- **Background:** Light red/warning background color to denote urgency
- **Sender Label:** "System Alert" or "Clinical Alert"
- **Typography:** Subject in bold or semi-bold to emphasize
- **Priority:** High
- **Examples:** Drug interaction warnings, medication safety alerts, clinical alerts

---

## Notes
- All timestamps should use relative time (e.g., "2 hours ago", "Yesterday") with tooltip showing absolute date/time
- Unread messages should be visually distinct through highlighting or a bold sender name
- Consider lazy-loading or pagination for performance when message volumes are high
- Message cards should be scannable with clear visual hierarchy
- Consider keyboard shortcuts for power users (e.g., arrow keys to navigate, "r" to reply)
- Message threading/conversations may be a future enhancement
