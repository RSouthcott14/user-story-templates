# say only UI: Consultation Complete Confirmation

**Story Type:** UI  
**Tags:** AI Generated, Review (BR)  
**DevOps Work Item:** [603164](https://dev.azure.com/NHS-Wales-Digital/Choose%20Pharmacy%20NextGen/_workitems/edit/603164)

---

## Description

### Story Card

**As a** pharmacy user  
**I want** to see confirmation that the consultation has been completed successfully  
**So that** I can verify the consultation record has been saved and understand the next steps for sending information to the GP

### Context

This story defines the **Consultation Complete Confirmation** page—the final screen displayed after a user successfully clicks "Complete consultation" on the Review Consultation Summary page. It confirms consultation completion and provides feedback on GP summary letter transmission, displaying success or error states accordingly.

The page displays:
- **Success confirmation** – "Consultation complete" with checkmark icon
- **GP summary letter status** – Success: "Summary letter sent to GP" OR Error: "GP consultation summary could not be sent electronically" with contingency instructions
- **Post-completion actions** – Print consultation, Print prescription, Get leaflets
- **Navigation** – Link to patient history to view the consultation record
- **High-level caption** – "Clinical Conditions Management" displayed at top

### Key Features

- **High-level caption** – "Clinical Conditions Management" identifying the consultation type
- **Consultation completion confirmation** – Clear visual confirmation with success icon and heading
- **GP summary letter transmission feedback** – Conditional display based on send success/failure
- **Error handling with guidance** – If letter fails to send, displays error message with contingency process instructions
- **Action buttons** – Print consultation, Print prescription, Get leaflets
- **Navigation link** – Link to "patient history" to access the saved consultation record

### Design Coverage

The page structure handles:
- **Success pathway** – Consultation complete + letter sent successfully
- **Error pathway** – Consultation complete + letter send failure with error message and contingency instructions
- **Post-completion actions** – Three action buttons with consistent styling

### Related Stories

| Story ID | Title | Type | Details |
| --- | --- | --- | --- |
| 603150 | UI: CCM - Review Consultation Summary – Complete Consultation | Related | Preceding page where user initiates completion |

### Dependent Stories

- Review Consultation Summary page must be completed before this page is displayed
- GP summary letter transmission backend API
- Print consultation functionality
- Print prescription functionality

---

## Design Reference

| Component | DHCW Design System V2 | Notes |
| --- | --- | --- |
| Page heading (h1) | [Headings - h1](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Consultation complete" |
| High-level caption | [Headings - h4](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "Clinical Conditions Management" |
| Success message box | [Alert - Success](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | Green background with checkmark icon |
| Success sub-message | [Body text](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "The patient's consultation record has been updated" |
| GP letter success | [Alert - Success](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | Green background: "Summary letter sent to GP" |
| GP letter error | [Alert - Error](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | Red border, error text with contingency instructions |
| Action buttons | [Button - Secondary](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | Print consultation, Print prescription, Get leaflets |
| Navigation link | [Link](https://www.figma.com/design/RplwUuFizhzeH1ng7M0SMO/DHCW-Design-System-V2) | "patient history" link to access consultation record |

---

## Acceptance Criteria

**Total Scenarios: 14**

### Page Structure & Content

**Scenario 1: Display consultation completion page**

```gherkin
Given a consultation has been successfully completed
When the consultation completion page is displayed
Then the page shall display a success confirmation banner
And the page shall display the message "Consultation complete"
And the page shall provide a link to the patient's consultation history
And the page shall display the available post-consultation actions based on consultation outcome and user permissions
```

**Scenario 2: Display standard action buttons**

```gherkin
Given the consultation completion page is displayed
When the page loads
Then the page shall display a "Print Consultation" button
```

### Default State

**Scenario 3: Consultation completion page default state**

```gherkin
Given the consultation completion page is displayed
When the page first loads
Then the consultation shall already be marked as completed
And no further consultation data entry shall be permitted from this page
And only the actions appropriate to the consultation outcome and user permissions shall be displayed
```

### User Interaction

**Scenario 4: Print consultation record**

```gherkin
Given the user is on the consultation completion page
When the user selects "Print Consultation"
Then a printable version of the completed consultation record shall be generated
And the user shall be able to print or save the document
```

**Scenario 5: Display Print Referral button for referral outcomes**

```gherkin
Given the consultation outcome is "Refer" or "Supply and Refer"
When the consultation completion page is displayed
Then a "Print Referral" button shall be displayed
```

**Scenario 6: Print referral document**

```gherkin
Given the consultation outcome is "Refer" or "Supply and Refer"
And the "Print Referral" button is displayed
When the user selects "Print Referral"
Then a printable referral document shall be generated
And the user shall be able to print or save the referral document
```

**Scenario 7: Hide Print Referral button for non-referral outcomes**

```gherkin
Given the consultation outcome is not "Refer" or "Supply and Refer"
When the consultation completion page is displayed
Then the "Print Referral" button shall not be displayed
```

**Scenario 8: Display Print Prescription button for eligible Independent Prescribers**

```gherkin
Given the consultation is an IPS pathway consultation
And the logged-in user is identified as an Independent Prescriber with permissions to generate prescriptions
When the consultation completion page is displayed
Then a "Print Prescription" button shall be displayed
```

**Scenario 9: Print prescription document**

```gherkin
Given the "Print Prescription" button is displayed
When the user selects "Print Prescription"
Then a printable prescription document shall be generated
And the user shall be able to print or save the prescription
```

**Scenario 10: Hide Print Prescription button for non-IP users**

```gherkin
Given the consultation is an IPS pathway consultation
And the logged-in user is not authorised to generate prescriptions
When the consultation completion page is displayed
Then the "Print Prescription" button shall not be displayed
```

**Scenario 11: Hide Print Prescription button for non-IPS pathways**

```gherkin
Given the consultation is not an IPS pathway consultation
When the consultation completion page is displayed
Then the "Print Prescription" button shall not be displayed
```

**Scenario 12: Access patient leaflets**

```gherkin
Given the consultation completion page is displayed
When the user selects "Get Leaflets"
Then the user shall be presented with the relevant patient information leaflets associated with the consultation
```

### Navigation

**Scenario 13: Navigate to patient history**

```gherkin
Given the consultation completion page is displayed
When the user selects the patient history link
Then the user shall be navigated to the patient's consultation history page
```

### Validation

**Scenario 14: Printing error handling**

```gherkin
Given the user selects any print action
When the document cannot be generated
Then an appropriate error message shall be displayed
And the consultation record shall remain unchanged
And the user shall be able to retry the action
```

---

## Notes

**Navigation Flow:**
- Entry point: Successful completion of Review Consultation Summary page
- Exit points: 
  - Print consultation / Print prescription / Get leaflets (actions on page)
  - Patient history link (navigate to consultation list)
  - Home / Patient search (via sidebar navigation)

**Success Criteria:**
- Consultation status changed to "Complete" in the system
- All consultation data finalized and locked
- Completion timestamp recorded for audit purposes
- GP summary letter sent (if selected), or error handled with contingency guidance
- User receives clear confirmation of completion

**Error Handling Strategy:**
- If GP letter send fails, error is displayed but does not prevent consultation completion
- User is guided to follow locally agreed contingency process for GP communication
- Consultation remains marked as complete regardless of letter send status
- User can still print consultation and access other post-completion actions

**Post-Completion Actions:**
- Print consultation: Full consultation record document
- Print prescription: Prescription data (if applicable to consultation type)
- Get leaflets: Educational materials relevant to condition/service
- Patient history: Access to consultation record and history

**Accessibility Considerations:**
- Success/error alerts use color + text (not color alone)
- Error message text is clear and actionable
- Print buttons include print icon + text label
- Patient information displays in structured format for screen readers
- Links have descriptive text ("patient history", not "click here")
