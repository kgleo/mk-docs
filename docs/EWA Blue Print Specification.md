Here is the comprehensive EWA Blueprint Specification with text-based BPMN 2.0 diagrams, grounded entirely in your internal documentation.

---

# EWA (Kyo Htote) — BPMN 2.0 Blueprint Specification

## 1. Overview

EWA (Earned Wage Access / Kyo Htote) is a digital advance salary service on the MoMoney platform. It enables corporate employees to access a portion of their earned wages before payday, disbursed to their MoMoney wallet or via OTC cash code at agents. [EWA Comprehensive Summary](https://team-1607663466339.atlassian.net/wiki/spaces/~712020f2b5537313354b4fa9732498d150e182/pages/1901166594/EWA+Earned+Wage+Access+Comprehensive+Summary+Document)

**8 Core Systems Involved:**

| # | System | Role |
|---|--------|------|
| 1 | Admin Portal (DW) | Corporate account creation, service fees config |
| 2 | Fun Flow Portal (Loan Unit) | EWA limits, amount style, repayment dates, late fees |
| 3 | MoBiz / Corporate Portal | Employee import, grant amounts, repayment, payroll |
| 4 | AMS / Backoffice Portal | KYC validation, employee verification, EWA approval |
| 5 | Subscriber App (MoMoney) | Employee-facing: EWA request, disbursement |
| 6 | DW Portal | Repayment management (Maker-Checker), offer changes |
| 7 | HR Portal | External company HR verification |
| 8 | Agent App | OTC cash-out via cash code |

---

## 2. End-to-End Process Architecture (5 Phases)

The EWA lifecycle consists of 5 phases, each modeled as a separate BPMN process that can be composed into a master landscape. [EWA 1+2 Steps](https://team-1607663466339.atlassian.net/wiki/spaces/~712020f2b5537313354b4fa9732498d150e182/pages/1920696330/EWA+1+2+Steps)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    EWA END-TO-END PROCESS LANDSCAPE                        │
│                                                                             │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐ │
│  │ Phase 1  │──▶│ Phase 2  │──▶│ Phase 3  │──▶│ Phase 4  │──▶│ Phase 5  │ │
│  │Corporate │   │Employee  │   │  EWA     │   │Disburse- │   │Repayment │ │
│  │Onboarding│   │Register &│   │ Request  │   │  ment    │   │& Arrears │ │
│  │& Config  │   │ Upgrade  │   │          │   │          │   │          │ │
│  └──────────┘   └──────────┘   └──────────┘   └──────────┘   └──────────┘ │
│       │              │              │              │              │         │
│       ▼              ▼              ▼              ▼              ▼         │
│  [Admin DW]     [MoBiz +      [Subscriber   [Wallet Core   [Corporate     │
│  [Fun Flow]      AMS + DW]      App]        + Agent App]    Portal + DW]  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Phase 1 — Corporate Onboarding & Configuration

**Actors:** MO Officer, MO Sale Team  
**Systems:** Admin Portal (DW), Fun Flow Portal, HR Portal  
**Source:** [EWA BRD V2](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/694092131/EWA+-+BRD+V2), [EWA Fees Config](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/639008862/EWA+Fees+Pricing+configuration)

### Text-Based BPMN Diagram

```
POOL: MoMoney Platform
├─ LANE: MO Officer (Admin Portal)
│  ├─ LANE: MO Officer (Fun Flow Portal)
│  └─ LANE: MO Sale Team (HR Portal)

  ●──▶[Create Corporate Account]──▶[Configure Service Fees]──▶◇ Fee Payer?
  Start    (DW Portal)                (DW Portal)               │
           Fill: org name,            Choose services:           ├─ Employee pays ──▶ Config 2 services:
           phone, email               • Sub Request Adv Salary   │   • Sub Request Adv Salary
           → Save                     • Corp Adv Salary Disb     │   • Corp Adv Salary Disb
           → Create portal            • Corp EWA Repayment       │
             credentials              Set Type: Standard/Fixed   ├─ Corporate pays ──▶ Config 3 services:
                                      Set %: e.g. 0.02 (2%)     │   (+ Corp EWA Repayment)
                                                                 │
                                                                 ▼
                                                    [Configure Limits & Calc]
                                                     (Fun Flow Portal)
                                                     Set: Amount Style
                                                     ├─ Percentage (10-50%)
                                                     ├─ Fixed Amount
                                                     └─ Date-based
                                                     Set: Repayment Due Date
                                                     Set: Late Fees
                                                       (0.1%/day ≤10 days,
                                                        0.2%/day >10 days)
                                                     Set: Contract Start/End
                                                                 │
                                                                 ▼
                                                    [Record Company in HR Portal]
                                                     (MO Sale Team)
                                                     Create HR user account
                                                     Send credentials to HR email
                                                                 │
                                                                 ▼
                                                              ◉ End
                                                     Corporate Onboarded
```

**Key Business Rules:**
- Each corporate has independent fee configuration (employee-paid vs corporate-paid)
- Amount style is per-corporate: Percentage of salary (e.g., 50% of 600,000 = 300,000 MMK), Fixed amount (e.g., 200,000 MMK for all), or Date-based calculation
- Late fees: 0.1%/day for first 10 days overdue, 0.2%/day thereafter

---

## 4. Phase 2 — Employee Registration & Offer Upgrade

**Actors:** Corporate HR, MO Officer, System (automated)  
**Systems:** MoBiz Portal, DW Portal, AMS  
**Source:** [EWA Auto Upgrade/Downgrade](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/810156085/EWA+Employee+Upload+Auto+Upgrade+Downgrade)

### Text-Based BPMN Diagram

```
POOL: MoMoney Platform
├─ LANE: Corporate HR (MoBiz Portal)
├─ LANE: System (Automated)
└─ LANE: MO Officer (DW Portal)

  ●──▶[HR Imports Employee Data]──▶[System Validates Subscriber]──▶◇ Wallet Registered?
  Start   (MoBiz Portal)              Check MoMoney wallet          │
          Bulk Excel or individual     registration status           │
          Employee ID, Name, Phone,                                  ├─ YES ──▶◇ Subscriber Level?
          NRC, Dept, Salary, etc.                                    │          │
                                                                     │          ├─ Sub Lv 2 ──▶[Auto Upgrade
                                                                     │          │                to Employee
                                                                     │          │                Group Lv 3]
                                                                     │          │                    │
                                                                     │          │                    ▼
                                                                     │          │          [Activate EWA Offer]
                                                                     │          │           (DW Portal)
                                                                     │          │           MO Officer changes
                                                                     │          │           offer to "EWA offer"
                                                                     │          │                    │
                                                                     │          │                    ▼
                                                                     │          │          [Send Notification]
                                                                     │          │           Employee receives
                                                                     │          │           EWA eligibility noti
                                                                     │          │                    │
                                                                     │          │                    ▼
                                                                     │          │                 ◉ End
                                                                     │          │            Employee EWA Ready
                                                                     │          │
                                                                     │          └─ Lv1/MoEmployee/
                                                                     │             Pending/Init/Closed
                                                                     │                    │
                                                                     │                    ▼
                                                                     │             [Exclude from
                                                                     │              auto upgrade]
                                                                     │                    │
                                                                     │                    ▼
                                                                     │                 ◉ End
                                                                     │              Not Eligible
                                                                     │
                                                                     └─ NO ──▶[Send SMS to Register]
                                                                                "Register MoMoney
                                                                                 wallet to access EWA"
                                                                                + APK download link
                                                                                       │
                                                                                       ▼
                                                                                    ◉ End
                                                                              Awaiting Registration
```

**Key Business Rules:**
- Auto upgrade: Sub Lv 2 + Active + Registered wallet → Employee Group Lv 3
- Auto downgrade: HR deletes employee → Lv 3 reverts to Lv 2, EWA access revoked
- One employee can belong to multiple corporates (independent EWA per corporate)

---

## 5. Phase 3 — EWA Advance Request (Employee Application)

**Actors:** Employee  
**Systems:** Subscriber App, Fun Flow (eligibility engine)  
**Source:** [EWA Feature List](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/760742055/EWA+Feature+List), [Subscriber App Demo](https://team-1607663466339.atlassian.net/wiki/spaces/~712020f2b5537313354b4fa9732498d150e182/pages/1911521281/Subscriber+MoMoney+App+Comprehensive+Demo+Script)

### Text-Based BPMN Diagram

```
POOL: Employee (Subscriber App)
POOL: MoMoney Platform
├─ LANE: Channel App (Subscriber App Backend)
├─ LANE: EWA Engine (Fun Flow Integration)
└─ LANE: Integrations

  ●──▶[Open Kyo Htote]──▶[Select Corporate]──▶[Auto-Display Info]──▶[Enter Amount]
  Start  Home screen       From corporate list    • Department          ≤ Available Amount
         "Advance Salary"  (employee may be       • Position
                           under multiple corps)  • Date
                                                  • Available Amount
                                                       │
                                                       ▼
                                              ◇ Validate Eligibility (Fun Flow)
                                              │  • Company Status
                                              │  • Company EWA Status
                                              │  • Department valid
                                              │  • Available Amount > 0
                                              │  • Allow Date (within cycle)
                                              │  • Repayment Status (no pending)
                                              │  • EWA Claim (not already claimed)
                                              │
                                    ┌─────────┼─────────┐
                                    │         │         │
                                    ▼         │         ▼
                              [Show Error]    │   [Display Fee Preview]
                              Messages:       │    • Requested Amount
                              • "Monthly      │    • Fee
                                limit         │    • Total Received =
                                reached"      │      Amount - Fee
                              • "Repayment    │         │
                                pending"      │         ▼
                              • "Not          │   [Select Payout Type]
                                eligible"     │    ├─ MoMoney Wallet
                                    │         │    └─ EWA OTC
                                    ▼         │         │
                                 ◉ End        │         ▼
                              Not Eligible    │   [Draw/Upload Signature]
                                              │    → Click "Agree"
                                              │         │
                                              │         ▼
                                              │   [Preview Screen]
                                              │    Staff Name, NRC,
                                              │    Company, Dept, Position,
                                              │    Date, Amount, Fee,
                                              │    Total Received
                                              │         │
                                              │         ▼
                                              │   [Confirm with PIN]
                                              │    6-digit password
                                              │         │
                                              │    ┌────┼────┐
                                              │    │         │
                                              │    ▼         ▼
                                              │ [PIN OK]  [PIN Wrong]
                                              │    │      "Incorrect.
                                              │    │       Try again."
                                              │    │      (5 fails = 30min lock)
                                              │    │
                                              │    ▼
                                              │ [Submit EWA Request]
                                              │    │
                                              │    ▼
                                              │ ◉ End → Proceed to Phase 4
                                              │   Request Submitted
```

**Key Business Rules:**
- **One EWA per month per employee per corporate** — 2nd attempt shows: "You have claimed Kyo Htote one time successfully. Please try again in next time."
- Employee under multiple corporates can claim once per corporate per month independently
- EWA allowable cycle: typically 10 days (15th–24th) per month (configurable per corporate)
- Calculation example: Basic Salary 600,000 × 50% = Available 300,000 MMK; Fee 1,500 MMK → Total Received 298,500 MMK

---

## 6. Phase 4 — Disbursement

**Actors:** System (automated), Agent (for OTC)  
**Systems:** Wallet Core, Agent App, SMS Gateway  
**Source:** [EWA Comprehensive Summary](https://team-1607663466339.atlassian.net/wiki/spaces/~712020f2b5537313354b4fa9732498d150e182/pages/1901166594/EWA+Earned+Wage+Access+Comprehensive+Summary+Document), [EWA OTC BRD](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/733642760/EWA+OTC+BRD)

### Text-Based BPMN Diagram

```
POOL: MoMoney Platform
├─ LANE: Disbursement Router
├─ LANE: Wallet Core
├─ LANE: OTC Engine
├─ LANE: GL Engine
└─ LANE: Notification Service

  ●──▶◇ Payout Type?
  Start │
        ├─ WALLET ─────────────────────────────────────┐
        │                                               │
        │  [Credit Employee Wallet]                     │
        │   Amount - Fees = Net Amount                  │
        │   e.g. 300,000 - 1,500 = 298,500 MMK         │
        │        │                                      │
        │        ▼                                      │
        │  [Success Screen]                             │
        │   "Total Received: 298,500 MMK"               │
        │                                               │
        ├─ OTC ────────────────────────────────────────┐│
        │                                              ││
        │  [Generate OTC Cash Code]                    ││
        │        │                                     ││
        │        ▼                                     ││
        │  [Send SMS with Cash Code]                   ││
        │   "Your Kyo Htote amount was                 ││
        │    successfully received.                    ││
        │    Secret code: XXXXXXXXXX                   ││
        │    Go to MoMoney agent to                    ││
        │    withdraw. Valid until XXXXX."              ││
        │        │                                     ││
        │        ▼                                     ││
        │  [Agent Cash Out] ◁── Employee visits agent  ││
        │   Agent App:                                 ││
        │   • Enter Cash Code                          ││
        │   • Enter Mobile Number                      ││
        │   • Validate & Dispense                      ││
        │                                              ││
        └──────────────────┬───────────────────────────┘│
                           │                            │
                           ▼                            │
              ═══════════════════════                   │
              ║  PARALLEL GATEWAY  ║                    │
              ═══════════════════════                   │
                    │           │                       │
                    ▼           ▼                       │
           [Post GL Entries]  [Send Notification]       │
            • Block e-money:   Push + In-App            │
              Debit 300,000    to employee              │
              from customer                             │
              → Credit to                               │
              L100000217                                │
              (Suspense GL)                             │
            • Send e-money:                             │
              Debit 292,500                             │
              from L100000217                           │
              → Credit to                               │
              customer                                  │
            • Release Fee:                              │
              Debit 7,500                               │
              from L100000217                           │
              → Credit to                               │
              I100000101                                │
              (Fee Income)                              │
                    │           │                       │
                    ▼           ▼                       │
              ═══════════════════════                   │
              ║  PARALLEL JOIN     ║                    │
              ═══════════════════════                   │
                         │                              │
                         ▼                              │
           [Update EWA Ledger]                          │
            Status: Active                              │
                         │                              │
                         ▼                              │
                      ◉ End                             │
                Advance Disbursed                       │
```

**GL Accounts:**
- **L100000217** — Suspense GL (holding account)
- **I100000101** — Fee Income
- **A100000201** — Bank Float (for settlement)

---

## 7. Phase 5 — Repayment & Arrears Management

**Actors:** Corporate HR, MO Officer (Operation Supervisor, Operation Manager)  
**Systems:** MoBiz Portal, DW Portal, Fun Flow  
**Source:** [EWA Repayment Management in DW](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/857112577/EWA+Repayment+Management+in+DW), [EWA Repayment Alert BRD](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/759825706/EWA+Repayment+Alert+BRD)

### Text-Based BPMN Diagram

```
POOL: MoMoney Platform
├─ LANE: Corporate HR (MoBiz Portal)
├─ LANE: MO Officer — Maker (DW Portal)
├─ LANE: MO Officer — Checker (DW Portal)
├─ LANE: System (Automated)
└─ LANE: GL Engine

  ●──▶◇ Repayment Method?
  Start │
        │
        ├─ METHOD A: Corporate Portal Repayment ──────────────────────┐
        │                                                              │
        │  [Login Corporate Portal]                                    │
        │       │                                                      │
        │       ▼                                                      │
        │  [View Pending Repayments]                                   │
        │   Advance Salary → Repayment                                 │
        │       │                                                      │
        │       ▼                                                      │
        │  [Select Transactions]                                       │
        │   Partial or Full                                            │
        │       │                                                      │
        │       ▼                                                      │
        │  [Submit] ──▶ [Maker-Checker Approval]                       │
        │                                                              │
        ├─ METHOD B: Payroll + Auto Repayment ────────────────────────┐│
        │                                                             ││
        │  [Upload Payroll File]                                      ││
        │   (Corporate Portal)                                        ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [System Auto-Calculates]                                   ││
        │   Net Salary = Basic Salary - EWA Repayment                 ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [Preview: Payroll + Repayment]                             ││
        │   • Portal Balance                                          ││
        │   • Net Salary Amount                                       ││
        │   • Net Kyo Htote Repayment                                 ││
        │   • Total Disbursement Amount                               ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [Submit] ──▶ [Checker Approve/Reject]                      ││
        │   Payroll disbursed + Repayment done simultaneously         ││
        │                                                             ││
        ├─ METHOD C: DW Portal Repayment (MO Side) ──────────────────┤│
        │                                                             ││
        │  [Maker: New Request → EWA Repayment]                      ││
        │   Select Company, Select Debit Type:                        ││
        │   • From Company Balance                                    ││
        │   • From Bank Float (KBZ/AYA/CB/YOMA/UAB)                  ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [Auto-Display Employee Details]                            ││
        │   EWA pending repayment data                                ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [Maker Submits]                                            ││
        │       │                                                     ││
        │       ▼                                                     ││
        │  [Checker Verifies & Approves]                              ││
        │                                                             ││
        └──────────────────┬──────────────────────────────────────────┘│
                           │                                           │
                           ▼                                           │
              ═══════════════════════                                  │
              ║  PARALLEL GATEWAY  ║                                   │
              ═══════════════════════                                  │
                    │           │                                      │
                    ▼           ▼                                      │
           [Post GL Entries]  [Update Status]                          │
            • Block: Debit     Pending → Paid                          │
              400,400 from                                             │
              customer                                                 │
              → Credit to                                              │
              L100000217                                               │
            • Send: Debit                                              │
              400,000 from                                             │
              L100000217                                               │
              → Credit to                                              │
              customer                                                 │
            • Fee: Debit                                               │
              400 from                                                 │
              L100000217                                               │
              → Credit to                                              │
              I100000101                                               │
                    │           │                                      │
                    ▼           ▼                                      │
              ═══════════════════════                                  │
              ║  PARALLEL JOIN     ║                                   │
              ═══════════════════════                                  │
                         │                                             │
                         ▼                                             │
                      ◉ End                                            │
                 Repayment Complete                                    │
```

### Arrears & Late Fees Sub-Process

```
  ⏰──▶◇ Repayment Due Date Reached?
  Timer  │
  Event  ├─ 3 days BEFORE due ──▶ [Send Repayment Reminder Alert]
         │                         to Corporate HR email
         │
         ├─ ON due date ──▶◇ Repayment Status?
         │                  │
         │                  ├─ Paid ──▶ ◉ End (Complete)
         │                  │
         │                  └─ Pending ──▶ [Start Late Fee Calculation]
         │                                  │
         │                                  ▼
         │                           ◇ Overdue Days?
         │                           │
         │                           ├─ ≤ 10 days ──▶ Late Fee = 0.1% × EWA Amount × Days
         │                           │
         │                           └─ > 10 days ──▶ Late Fee = 0.2% × EWA Amount × Days
         │                                              │
         │                                              ▼
         │                                    [Update Total Repayment]
         │                                     Total = EWA Amount + Late Fees + Service Charges
         │                                              │
         │                                              ▼
         └─ 1 day AFTER due ──▶ [Send Overdue Alert]    │
                                 to Corporate HR         │
                                                         ▼
                                               [Employee App Status]
                                                Status: "Overdue"
                                                • Kyo Htote Amount
                                                • Repayment Date
                                                • Overdue Days
```

---

## 8. EWA Application Status Lifecycle

```
  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
  │ Pending  │──▶│ Approved │──▶│ Ongoing  │──▶│   Paid   │    │ Overdue  │
  │          │    │          │    │          │    │          │    │          │
  │ During   │    │ After    │    │ Between  │    │ When EWA │    │ After    │
  │ review   │    │ R&C      │    │ disburse │    │ amount   │    │ repayment│
  │ process  │    │ approval │    │ & repay  │    │ is repaid│    │ date if  │
  │          │    │          │    │ date     │    │          │    │ not paid │
  └──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
       │                                                              │
       │                              ┌──────────┐                    │
       └─────────────────────────────▶│  Failed  │◁───────────────────┘
                                      │          │    (if system error
                                      │ Any step │     or rejection)
                                      │ fails    │
                                      └──────────┘
```

Source: [EWA Individual 2025](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1326055425/EWA+Kyo+Htote+Individual+2025+Version)

---

## 9. Individual EWA Flow (2025 Version) — Multi-Stage Verification

For individual EWA (not corporate-batch), there's a multi-stage AMS review process. [EWA Individual 2025](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1326055425/EWA+Kyo+Htote+Individual+2025+Version)

```
POOL: Employee
POOL: MoMoney Platform
├─ LANE: Operation (AMS)
├─ LANE: CSU / Credit Team (AMS)
├─ LANE: Company HR (HR Portal)
├─ LANE: R&C / Compliance (AMS)
└─ LANE: MO Officer (DW Portal)

  ●──▶[Employee Fills Application]──▶[Submit to AMS]
  Start  (Subscriber App)              │
         Personal info, job info,       ▼
         upload documents         [Operation: KYC Validation]
                                   Verify identity docs
                                        │
                                        ▼
                                  [CSU: Verify Employee Info]
                                   Salary, company status,
                                   active employment
                                        │
                                        ▼
                                  [Company HR: Verify Employee]
                                   (HR Portal)
                                   Overall check → Verified/Reject
                                        │
                                   ┌────┼────┐
                                   │         │
                                   ▼         ▼
                              [Verified]  [Rejected]
                                   │         │
                                   ▼         ▼
                              [R&C: Final Decision]  ◉ End
                               Assess KYC,           Rejected
                               legitimacy,
                               customer profiling
                                   │
                              ┌────┼────┐
                              │         │
                              ▼         ▼
                         [Approved]  [Rejected]
                              │         │
                              ▼         ▼
                    [MO Officer:     ◉ End
                     Change Offer    Rejected
                     to "EWA offer"]
                     (DW Portal)
                              │
                              ▼
                    [Send Approval Notification]
                     "Congratulations. You can
                      apply Kyo Htote on the
                      date between 10 to 25
                      of the month."
                              │
                              ▼
                           ◉ End
                     Employee Can Apply EWA
```

---

## 10. Validation Rules Summary

| Rule | Description | Error Message |
|------|-------------|---------------|
| One claim per month | Employee can only claim once per month per corporate | "You have claimed Kyo Htote one time successfully. Please try again in next time." |
| Repayment pending | Cannot apply if previous repayment is pending | "Your repayment is in pending. Please contact your Administrator." |
| Company not enabled | Corporate hasn't enabled EWA | "EWA feature is not enabled for this company." |
| Amount exceeds limit | Requested > Available | "You can apply maximum [Available Amount]. Please try again with lower amount." |
| Outside allowable dates | Not within EWA cycle window | "Please check with your company HR to access Kyo Htote services." |
| PIN incorrect (5x) | Account locked 30 minutes | "You have made 5 incorrect attempts. Account locked for 30 minutes." |

---

## 11. Partner API Endpoints (WageFlow Integration)

For the upcoming WageFlow backend migration: [JITS EWA Proposal](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1906769922/JITS+Innovation+Labs+Proposal+for+EWA+Platform+Implementation)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    PARTNER API (REST)                                │
│                                                                     │
│  Auth: OAuth 2.0 + HMAC-SHA256 | Rate: 100 req/min | Idempotent   │
│                                                                     │
│  GET  /api/v1/employees/{id}/eligibility  → EWA eligibility check  │
│  GET  /api/v1/employees/{id}/balance      → Real-time earned wage  │
│  POST /api/v1/advances                    → Submit advance request │
│  GET  /api/v1/advances/{txId}/status      → Check advance status   │
│  GET  /api/v1/employees/{id}/transactions → Transaction history    │
│  GET  /api/v1/fee-schedule                → Fee structure          │
│  GET  /api/v1/disbursement/destinations   → Supported banks/wallets│
│                                                                     │
│  Webhooks: Real-time push via HTTPS endpoints                      │
│  Replay window: 5 minutes                                          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 12. EWA Calculation Examples

```
┌─────────────────────────────────────────────────────────────────┐
│ EXAMPLE 1: Percentage-based (50%)                               │
│                                                                 │
│ Basic Salary:     600,000 MMK                                   │
│ Corporate Config: 50% allowable                                 │
│ Available Amount: 600,000 × 50% = 300,000 MMK                  │
│ Service Fee:      1,500 MMK (flat) or 2% = 6,000 MMK           │
│ Total Received:   300,000 - 1,500 = 298,500 MMK (flat fee)     │
│                                                                 │
│ EXAMPLE 2: Fixed amount                                         │
│                                                                 │
│ Corporate Config: Fixed 200,000 MMK                             │
│ Available Amount: 200,000 MMK (regardless of salary)            │
│ Service Fee:      200 MMK (flat)                                │
│ Total Received:   200,000 - 200 = 199,800 MMK                  │
│                                                                 │
│ EXAMPLE 3: Late fee calculation                                 │
│                                                                 │
│ EWA Amount:       300,000 MMK                                   │
│ Overdue:          15 days                                       │
│ Late Fee (1-10):  300,000 × 0.1% × 10 = 3,000 MMK              │
│ Late Fee (11-15): 300,000 × 0.2% × 5  = 3,000 MMK              │
│ Total Late Fee:   6,000 MMK                                    │
│ Total Repayment:  300,000 + 6,000 + Service Charges             │
└─────────────────────────────────────────────────────────────────┘
```

---

## 13. Use Case Matrix

| UC ID | Use Case | Channel | Actor |
|-------|----------|---------|-------|
| EWA_01 | Employee apply EWA via Wallet | Subscriber App | Employee |
| EWA_02 | Employee apply EWA OTC | Subscriber App | Employee |
| EWA_03 | Agent cash out EWA OTC with cash code | Agent App | Agent |
| EWA_04 | Corporate manages/turn on-off EWA | Corporate Portal | Corporate Admin |
| EWA_05 | Partial Repayment | Corporate Portal | Corporate HR |
| EWA_06 | Auto Payroll & EWA Repayment (Wallet) | Corporate Portal | Corporate HR |
| EWA_07 | Auto Payroll & EWA Repayment (OTC) | Corporate Portal | Corporate HR |
| EWA_08 | Set limit amount & dates | Fun Flow Portal | MO Officer |
| EWA_09 | Set fees on Admin Portal | Admin Portal | MO Officer |

Source: [EWA Feature List](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/760742055/EWA+Feature+List)

---

This blueprint covers the complete EWA lifecycle from corporate onboarding through disbursement to repayment, with all validation rules, GL flows, and system responsibilities mapped to BPMN-ready process structures. Each phase can be directly translated into executable BPMN 2.0 XML using the patterns from the previous XMLs I generated.

Would you like me to create a Confluence page with this content in your personal space, or generate additional BPMN 2.0 XML files for any specific phase (e.g., the repayment process or the individual EWA multi-stage verification)?