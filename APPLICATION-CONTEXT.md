# Choose Pharmacy NextGen - Application Context

**Application:** Choose Pharmacy NextGen  
**Organization:** NHS Wales Digital (DHCW)  
**Purpose:** Provide community pharmacies with a digital platform to deliver specialized pharmacy services across Wales

---

## 📋 Overview

Choose Pharmacy NextGen is a comprehensive digital solution that enables community pharmacies to record consultations and provide multiple NHS-commissioned services. The application streamlines service delivery, improves patient safety through better information sharing, and generates referrals and clinical summaries for GP records.

**Key Features:**
- Multi-service consultation platform
- Welsh GP record integration
- Electronic discharge information transfer
- Automated referral letter generation
- Patient history tracking
- Clinical decision support

---

## 🏥 Services Provided

### 1. Common Ailments Service (CAS)

**Purpose:** Record consultations for patients presenting with common ailments and generate referrals where needed.

**What it does:**
- Records consultation with patient presenting with a common ailment
- Generates referral letter if patient needs alternative NHS service access within 48 hours
- Creates summary of consultation for GP records
- Provides clinical assessment and advice

**28 Common Ailments Supported:**

| ID  | Ailment Name |
|-----|---|
| 1   | ACNE VULGARIS |
| 2   | ATHLETE'S FOOT |
| 3   | BACK PAIN |
| 4   | CHICKENPOX (IN CHILDREN UNDER 14 YEARS OF AGE) |
| 5   | COLD SORES |
| 6   | COLIC (INFANTILE) |
| 7   | CONJUNCTIVITIS (BACTERIAL) |
| 8   | CONSTIPATION |
| 9   | DIARRHOEA |
| 10  | DRY EYES DISEASE |
| 11  | DRY SKIN INCLUDING DERMATITIS AND ATOPIC ECZEMA |
| 12  | HAEMORRHOIDS |
| 13  | ALLERGIC RHINITIS |
| 14  | HEAD LICE |
| 15  | DYSPEPSIA |
| 16  | INGROWING TOENAIL |
| 18  | MOUTH ULCERS (SIMPLE APHTHOUS) |
| 19  | NAPPY RASH |
| 20  | ORAL THRUSH |
| 21  | RINGWORM, TINEA CRURIS AND INTERTRIGO |
| 22  | SCABIES |
| 23  | SORE THROAT |
| 24  | TEETHING |
| 25  | THREADWORMS |
| 26  | VULVOVAGINAL THRUSH |
| 27  | WARTS AND VERRUCAE |
| 28  | URINARY TRACT INFECTION (UTI) |

**User Flow:**
1. Patient presents with common ailment
2. Pharmacist selects/assesses condition
3. Consultation recorded in system
4. Clinical assessment performed
5. If referral needed: Referral letter generated (within 48 hours)
6. Summary sent to GP records

---

### 2. Sore Throat Test and Treat (STTT)

**Purpose:** Provide clinical assessment and throat swab testing for sore throat patients.

**What it does:**
- Clinical assessment for patients with sore throat
- Simple throat swab test for suspected bacterial infection
- Antibiotics prescribed if appropriate following positive test
- Results recorded in patient record

**User Flow:**
1. Patient presents with sore throat
2. Pharmacist performs clinical assessment
3. If indicated: Throat swab test performed
4. Test results assessed
5. If positive: Antibiotics prescribed/dispensed
6. Consultation summary recorded

---

### 3. Urinary Tract Infection (UTI)

**Purpose:** Manage uncomplicated lower UTI cases in community pharmacy setting.

**What it does:**
- Community pharmacy management of uncomplicated lower UTI
- Aligned to NHS Wales Common Ailments Service
- Functions as sub-section of CAS
- Clinical assessment and medication provision
- Advice and support for eligible CAS patients

**User Flow:**
1. Patient presents with UTI symptoms
2. Pharmacist performs clinical assessment
3. Eligibility checked (CAS pathway)
4. Advice provided and/or medication prescribed
5. Consultation recorded
6. GP summary generated if needed

---

### 4. Discharge Medicines Review (DMR)

**Purpose:** Reconcile medicines following hospital discharge and prevent medication errors.

**What it does:**
- Receives electronic discharge information from Hospitals (MTeD system)
- Transfers discharge data to patient's nominated Community Pharmacy
- Enables pharmacy to undertake Electronic Discharge Medicines Review
- Identifies medication discrepancies
- Reconciles patient medicines post-discharge

**Key Benefits:**
- Minimizes unintentional medication changes at care transitions
- Prevents incorrect dosage or omission of medications
- Returns £3 for every £1 invested through:
  - Reduced A&E attendances
  - Avoided hospital admissions
  - Reduced medicines waste
- Streamlines process vs. manual referral methods
- Easier identification of discharged patients

**User Flow:**
1. Hospital discharge information received via MTeD
2. Discharged patient data imported to pharmacy system
3. Pharmacist notified of patient discharge
4. Medicines reconciliation performed
5. Discrepancies identified and documented
6. Resolution actions recorded
7. Summary sent to GP

---

### 5. Emergency Medicines Supply (EMS)

**Purpose:** Provide emergency medicine supplies when GP access unavailable.

**What it does:**
- Supplies emergency medicines not available from patient's GP
- Access to Welsh GP record summary for accurate prescribing
- Used when patient on holiday or medication needed outside GP hours
- Emergency dispensing decisions supported by GP record data

**Common Use Cases:**
- Patient on holiday needs repeat medication
- Medication required outside GP surgery hours
- Urgent supply needed due to unexpected circumstances
- Weekend/bank holiday access to medicines

**User Flow:**
1. Patient requests emergency medicine supply
2. Pharmacist accesses Welsh GP record
3. Medication history and eligibility reviewed
4. Emergency supply determined appropriate
5. Consultation recorded
6. Medicine dispensed
7. GP notified of supply

---

### 6. Contraception Service (CS)

**Purpose:** Provide emergency and bridging/quickstart contraception in community pharmacy.

**What it does:**
- For accredited Community Pharmacists in commissioned pharmacies
- Record consultations for emergency contraception supply
- Record consultations for bridging/quickstart contraception
- Electronic claims submission from system
- Consultation documentation

**Eligibility:**
- Accredited Community Pharmacists
- Pharmacy commissioned for NHS Wales CS enhanced service
- Eligible patients requiring emergency or bridging contraception

**User Flow:**
1. Patient presents for contraception service
2. Pharmacist assesses patient eligibility
3. Consultation recorded
4. Service provided (emergency/bridging contraception)
5. Electronic claim generated
6. Claim submitted within system

---

### 7. Independent Prescribers Service (IPS)

**Purpose:** Enable qualified independent prescriber pharmacists to record consultations and prescribe autonomously.

**What it does:**
- For pharmacists qualified as Independent Prescribers
- Record consultations within Choose Pharmacy
- Prescribe autonomously within clinical competence
- Access to Welsh GP Record for clinical decision support
- Self-management advice provision
- Referrals to GP/A&E/Out-of-Hours when appropriate
- Summary letter generated for patient's GP
- Patient history created and portable across pharmacies

**Scope:**
- Pharmacist can prescribe for any condition within their clinical competence
- No requirement for GP approval
- Direct access to patient history
- Portable patient record across IPS-providing pharmacies

**User Flow:**
1. Patient presents for consultation
2. Pharmacist performs clinical assessment
3. Welsh GP record accessed
4. Clinical decision made
5. If prescribing: Medication prescribed within competence
6. If referral needed: Patient referred to GP/A&E/OOH
7. Advice for self-management provided
8. Summary letter generated for GP
9. Patient history created/updated

---

## 🔑 Key Concepts

### Clinical Conditions Management (CCM)

The CCM journey refers to the user interface and workflow for managing clinical conditions throughout the application. It encompasses:

- **Condition Selection** — Identifying and selecting the patient's condition
- **Symptom Assessment** — Gathering patient symptom information
- **Clinical Assessment** — Pharmacist's clinical evaluation
- **Decision Points** — Prescribe, refer, or advice only
- **Template Selection** — Choosing appropriate treatment/management template
- **Outcome Recording** — Documenting the consultation outcome

### Common Pathways

**CAS Pathway** — Used for Common Ailments Service consultations  
**STTT Pathway** — Used for Sore Throat Test and Treat  
**UTI Pathway** — Used for Urinary Tract Infection (sub-section of CAS)  
**DMR Pathway** — Used for Discharge Medicines Reviews  
**EMS Pathway** — Used for Emergency Medicines Supply  
**CS Pathway** — Used for Contraception Service  
**IPS Pathway** — Used for Independent Prescriber consultations  

---

## 👥 User Roles

### Pharmacy User (Community Pharmacist)

**Permissions:**
- Access to assigned pharmacy records
- Record consultations for all applicable services
- Access to Welsh GP record (where eligible)
- Generate referrals and summaries
- Dispense medicines
- Create patient history records

**Responsibilities:**
- Clinical assessment of patients
- Appropriate service pathway selection
- Accurate consultation recording
- Appropriate referrals
- GP communication

### Pharmacy Manager

**Permissions:**
- View pharmacy performance metrics
- Manage staff and access levels
- Configure pharmacy settings
- View pharmacy reports

### GP / Medical Records Team

**Permissions:**
- View consultation summaries
- Access referral letters
- Update GP records with pharmacy actions
- Review DMR submissions

---

## 🔗 Data Integration Points

### Welsh GP Record Integration
- Read-only access to patient GP records
- Used for clinical decision-making
- Supports medication reconciliation
- Improves clinical safety

### Medicines Transcribing and e-Discharge (MTeD) System
- Hospital discharge information transfer
- Automated data import for DMR
- Reduces manual data entry
- Improves accuracy

### Referral Management
- Electronic referral letter generation
- Automated routing to appropriate services
- Tracks referral outcomes
- GP record updates

---

## 📊 Key Metrics & Outcomes

- **Service Utilization** — Number of consultations per service type
- **Referral Rate** — % of patients requiring further intervention
- **DMR Benefits** — Medication discrepancies identified and resolved
- **Patient Safety** — Adverse events prevented through early intervention
- **Efficiency Gains** — Time saved vs. manual processes
- **Cost Savings** — £3 returned per £1 invested (DMR service)

---

## 🎯 Design Principles for User Stories

When creating user stories for Choose Pharmacy NextGen, consider:

1. **Clinical Accuracy** — Stories must reflect real clinical workflows
2. **Patient Safety** — Features must support safe pharmacy practice
3. **Data Integration** — Stories should account for GP record access
4. **Accessibility** — Stories must meet WCAG 2.1 AA standards
5. **Consistency** — UI components must match DHCW Design System V2
6. **Pathways** — Stories should align with appropriate service pathway
7. **User Context** — Stories must reflect community pharmacy environment
8. **Regulatory Compliance** — Stories should support NHS regulations

---

## 📞 Questions?

- **Service specifics?** Refer to the detailed service sections above
- **Pathway selection?** Check the Key Concepts section
- **User roles?** See User Roles section
- **Design components?** Reference COMPONENTS.md
- **Story structure?** Use templates in /templates/ folder

---

**Last Updated:** 2026-09-15  
**Application:** Choose Pharmacy NextGen  
**Organization:** NHS Wales Digital (DHCW)

