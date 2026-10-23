# Messages Screen - Design Mockup & Visual Specifications

## Screen Layout Overview

```
┌─────────────────────────────────────────────────────────────┐
│  Home    Patient search    Reports    Help    System settings │  ← Navigation
├──────────────────────────────────────────────────────────────┤
│ ← Back                                            Gillian Williams │  ← Header
├──────────────────────────────────────────────────────────────┤
│                                                                  │
│  LEFT SIDEBAR (200px)        MAIN CONTENT AREA                 │
│  ┌──────────────────┐  ┌───────────────────────────────────┐   │
│  │ FILTER PANEL     │  │ Messages              [4]          │   │
│  │                  │  │                                   │   │
│  │ ◉ All (4)        │  │ ┌─────────────────────────────┐   │   │
│  │ ○ System (1)     │  │ │ 🔵 System Administrator      │   │   │
│  │ ○ Patients (1)   │  │ │                              │   │   │
│  │ ○ Alerts (2)     │  │ │ System Maintenance Scheduled │   │   │
│  │ ○ Referrals (0)  │  │ │                              │   │   │
│  │                  │  │ │ Scheduled maintenance on     │   │   │
│  │                  │  │ │ Nov 23, 2:00 AM              │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ 2 hours ago              ●   │   │   │
│  │                  │  │ └─────────────────────────────┘   │   │
│  │                  │  │                                   │   │
│  │                  │  │ ┌─────────────────────────────┐   │   │
│  │                  │  │ │ 🟢 Patient - James Smith    │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ Follow-up question about     │   │   │
│  │                  │  │ │ medication dosage            │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ 4 hours ago              ●   │   │   │
│  │                  │  │ └─────────────────────────────┘   │   │
│  │                  │  │                                   │   │
│  │                  │  │ ┌─────────────────────────────┐   │   │
│  │                  │  │ │ 🔴 ALERT - Drug Interaction │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ Potential interaction        │   │   │
│  │                  │  │ │ detected for consultation    │   │   │
│  │                  │  │ │ #12847                       │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ 1 day ago                    │   │   │
│  │                  │  │ └─────────────────────────────┘   │   │
│  │                  │  │                                   │   │
│  │                  │  │ ┌─────────────────────────────┐   │   │
│  │                  │  │ │ 🟣 Referral - Dr. Sarah Jones│  │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ Specialist referral for CCM  │   │   │
│  │                  │  │ │ patient follow-up            │   │   │
│  │                  │  │ │                              │   │   │
│  │                  │  │ │ 2 days ago                   │   │   │
│  │                  │  │ └─────────────────────────────┘   │   │
│  │                  │  │                                   │   │
│  │                  │  │ Showing 1-4 of 4                 │   │
│  │                  │  │ ← Previous    Next →              │   │
│  └──────────────────┘  └───────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────┘
```

---

## Message Type Visual Specifications

### 1. System Administrator Message
- **Icon:** 🔵 Blue circle with gear icon
- **Border/Accent:** Subtle blue left border (3-4px)
- **Background:** Light blue tint or standard white
- **Sender Label:** "System Administrator" (non-clickable text)
- **Typography:** Standard body text, subject in semi-bold
- **Badge:** Optional "System" tag if space allows

**Example Content:**
```
🔵 System Administrator

System Maintenance Scheduled

The system will undergo scheduled maintenance on November 23, 2024 
from 2:00 AM to 3:30 AM. No services will be available during this time.

2 hours ago
```

---

### 2. Patient Message
- **Icon:** 🟢 Green circle with person/chat icon
- **Border/Accent:** Subtle green left border (3-4px)
- **Background:** Light green tint or standard white
- **Sender Label:** Patient first and last name (clickable to view patient record)
- **Typography:** Standard body text, subject in semi-bold
- **Patient Identifier:** Optional: Patient ID or NHS number in gray text

**Example Content:**
```
🟢 Patient - James Smith
NHS: 123 456 7890

Follow-up question about medication dosage

Hi, I'm taking the new medication but I'm unsure about the 
correct dosage timing. Should I take it with food?

4 hours ago
```

---

### 3. Alert Message
- **Icon:** 🔴 Red circle with exclamation mark
- **Border/Accent:** Prominent red left border (4px) - more prominent than others
- **Background:** Light red/warning background color to denote urgency
- **Sender Label:** "System Alert" or "Clinical Alert"
- **Typography:** Subject in bold or semi-bold to emphasize
- **Visual Treatment:** Consider slight shadow or outline to stand out
- **Styling:** Similar to Notification Banner component (alert variant)

**Example Content:**
```
🔴 ALERT - Drug Interaction Warning

Potential Interaction Detected

A potentially serious drug interaction has been identified for 
consultation #12847. Patient is taking:
- Warfarin (anticoagulant)
- Naproxen (NSAID)

REQUIRES IMMEDIATE REVIEW

1 day ago
```

---

### 4. Referral Message
- **Icon:** 🟣 Purple circle with arrow or exchange icon
- **Border/Accent:** Subtle purple left border (3-4px)
- **Background:** Light purple tint or standard white
- **Sender Label:** Healthcare provider name and organization
- **Typography:** Standard body text, subject in semi-bold
- **Referral Status:** Optional badge showing "Pending Response", "Accepted", etc.

**Example Content:**
```
🟣 Referral - Dr. Sarah Jones
Wales Community Health NHS Trust

Specialist Referral for Clinical Conditions Management

Patient James Smith has been referred for a CCM consultation 
follow-up. Referral details and consultation notes are attached.

Status: Pending Response

2 days ago
```

---

## Interaction States

### Message Card States

#### Unread Message (Default)
- **Visual Indicator:** Blue dot (●) on right side OR highlighted background
- **Text Styling:** Sender name in semi-bold
- **Background:** Slightly highlighted (light gray or light blue tint)

#### Read Message
- **Visual Indicator:** No dot indicator
- **Text Styling:** Standard font weight
- **Background:** Standard white or light gray

#### Hovered Message
- **Background:** Slight shadow or light highlight
- **Cursor:** Pointer
- **Transition:** Smooth 200ms transition

#### Active/Selected Message
- **Background:** Light blue or primary color
- **Border:** Left border highlighted in primary color
- **Shadow:** Subtle box-shadow to indicate elevation

#### Message Detail View (Expanded)
- **Full Message Body:** Displayed with proper line height and spacing
- **Meta Information:** Sender, timestamp, message type clearly visible
- **Action Buttons:** "Reply" (primary button), "Archive", "Mark as Unread"
- **Back Button:** Return to message list

---

## Filter Panel Specifications

### Layout
- **Width:** 200px fixed
- **Background:** Light gray (slightly different from main content)
- **Padding:** 16px
- **Border:** Right border or subtle separation

### Filter Options
1. **All** (shows count: e.g., "All (4)")
   - Selected by default
   - Radio button style

2. **System** (shows count: e.g., "System (1)")
   - Only system administrator messages

3. **Patients** (shows count: e.g., "Patients (1)")
   - Only patient messages

4. **Alerts** (shows count: e.g., "Alerts (2)")
   - Only alert messages (red/urgent)

5. **Referrals** (shows count: e.g., "Referrals (0)")
   - Only referral messages

### Styling
- **Radio Button:** Standard DHCW radio control
- **Label:** Clickable to select filter
- **Selected State:** Radio filled, text bold
- **Count Badge:** Gray text in parentheses
- **Hover State:** Light background highlight

---

## Unread Message Counter

- **Location:** Top right of "Messages" heading or in badge format
- **Format:** "[X] unread" or just "[X]" in a badge
- **Styling:** 
  - Background: Primary color (blue)
  - Text: White
  - Border-radius: 12px
  - Padding: 2px 8px
  - Font size: 12px-14px
- **Behavior:** 
  - Updates in real-time as messages are marked read
  - Hidden when count is 0
  - Animates when new messages arrive (optional pulse or fade)

---

## Pagination Controls

- **Location:** Bottom of message list
- **Format:** "Showing 1-10 of 45" with "← Previous" and "Next →" buttons
- **Button States:**
  - Disabled on first page (Previous button)
  - Disabled on last page (Next button)
  - Active/clickable otherwise
- **Page Size:** 10 messages per page (adjustable if needed)

---

## Empty State

When there are no messages:

```
┌─────────────────────────────────────┐
│                                     │
│           [Envelope Icon]           │
│                                     │
│         No messages yet              │
│                                     │
│    You don't have any messages.     │
│    Check back later.                │
│                                     │
└─────────────────────────────────────┘
```

---

## Responsive Behavior

### Desktop (1024px+)
- Left sidebar filter panel: 200px fixed
- Main content: flexible width
- Two-column layout maintained

### Tablet (768px - 1023px)
- Left sidebar: Collapsible/drawer style (hamburger menu)
- Main content: Full width when sidebar closed
- Message cards: Same styling, adjusted padding

### Mobile (< 768px)
- No sidebar visible by default
- Filter options accessible via dropdown or bottom sheet
- Message cards: Full width with adjusted padding
- Stack vertically: Back link, Filter dropdown, Message list

---

## Color Palette & Icons

| Message Type | Color    | Icon       | Badge Color |
|--------------|----------|-----------|-------------|
| System       | Blue     | ⚙️ Gear   | #0066CC     |
| Patient      | Green    | 👤 Person  | #28A745     |
| Alert        | Red      | ⚠️ Warning | #DC3545     |
| Referral     | Purple   | ↔️ Arrow   | #6F42C1     |

---

## Accessibility Considerations

1. **Focus Indicators:** Clear 2px focus ring on all interactive elements
2. **Color Contrast:** All text meets WCAG AA standards (4.5:1 minimum)
3. **Icon Alt Text:** All icons have aria-labels
4. **Keyboard Navigation:** Tab through filters → message cards → pagination
5. **Screen Reader:** Announces message type, sender, and unread status
6. **Timestamps:** Tooltip on hover shows full date/time
7. **Message Type:** Aria-label announces message type: "System message from Administrator", "Patient message from James Smith", etc.

---

## Animation & Transitions

- **Message Loading:** Fade in (200ms)
- **Card Hover:** Background color shift (150ms ease)
- **Filter Selection:** Highlight change (100ms)
- **Unread Badge Update:** Counter increments with subtle animation
- **Error Messages:** Slide down from top with 300ms duration
- **Expand/Collapse:** Message detail expand/collapse (300ms)

---

## Notes for Development

1. Consider infinite scroll as alternative to pagination for mobile
2. Implement auto-refresh for new messages (every 30-60 seconds)
3. Add loading skeleton while messages fetch
4. Cache message list state for fast back navigation
5. Consider swipe actions on mobile (e.g., swipe left to archive)
6. Implement message grouping by date if list grows large
