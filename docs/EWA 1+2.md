Here's a comprehensive breakdown of the **EWA 2.0 / EWA 2025 (Kyo Htote Individual)** project — step-by-step activities, process flow, responsibilities, and key decisions — compiled from all your Confluence documentation.

---

## 📋 EWA 2.0 Project Overview

**Full Name:** EWA (Earned Wage Access / Kyo Htote) Individual 2025 Version
**Objective:** Allow employees to apply for advance salary via MoMoney App with multi-stage verification (Operation → Credit → HR → R&C), reducing HR workload.
**BRD Authors:** Mai Khin Moe Moe Myat & Su Su Myaing | **Created:** 2025-02-18

---

## 🔄 End-to-End Process Table of Responsibility

### Phase 1 — Corporate Onboarding & Configuration

| Step | Activity                    | Channel/System              | Owner/Actor  | What Needs to Be Done                                                                                                                                 | Decision Point                                     | Status/Timeline      |
| ---- | --------------------------- | --------------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- | -------------------- |
| 1.1  | Create Corporate Account    | Admin Portal (DW)           | MO Officer   | Log in DW → Corporate → Create → Fill org name, phone, email → Save → Create portal credentials                                                       | —                                                  | Done (per corporate) |
| 1.2  | Service Fees Configuration  | Admin Portal (DW)           | MO Officer   | Go to corporate → Action → Fee Config → Set fee type (Standard 2% or Fixed), set who pays (Employee or Corporate)                                     | **Decision:** Employee-paid vs Corporate-paid fees | Config per corporate |
| 1.3  | Limits & Calculation Config | Fun Flow Portal (Loan Unit) | MO Officer   | Dataset → Corporate List → Create → Set Amount Style (% or Fixed), Repayment Due Date, Late Fees (0.1%/day first 10 days, 0.2% after), Contract Dates | **Decision:** % of salary (10-50%) or fixed amount | Config per corporate |
| 1.4  | Record Company in HR Portal | HR Portal (Backoffice)      | MO Sale Team | Record company list, create HR user account, send login credentials to company HR's email                                                             | —                                                  | New for EWA 2.0      |

### Phase 2 — Employee Registration & Setup

| Step | Activity | Channel/System | Owner/Actor | What Needs to Be Done | Decision Point | Status/Timeline |
|------|----------|----------------|-------------|----------------------|----------------|-----------------|
| 2.1 | Employee Import | MoBiz / Corporate Portal | MO Officer / Corporate HR | Import employee data (bulk Excel or individual) → System validates wallet registration | — | Per corporate |
| 2.2 | Auto Upgrade/Downgrade | System (Auto) | System | If Level 2 + registered → Auto upgrade to Employee Level 3; If deleted → downgrade Level 3→2 | Auto | Automated |
| 2.3 | EWA Offer Activation | DW Portal | MO Officer | Search subscriber → Change offer to "EWA offer" → Employee receives notification | — | Per employee |

### Phase 3 — EWA Application (Individual 2025 Flow — NEW)

| Step | Activity | Channel/System | Owner/Actor | What Needs to Be Done | Decision Point | Status/Timeline |
|------|----------|----------------|-------------|----------------------|----------------|-----------------|
| 3.1 | Employee Submits EWA Request | Subscriber App (MoMoney) | Employee | Login → Select Kyo Htote → Request EWA → Fill personal info, job info, upload NRC, employee proof, e-signature, HR recommendation letter → Accept T&C → Submit | — | Employee-initiated |
| 3.2 | KYC Validation | AMS Portal | **Operation Manager (OM)** | Review submitted application → Verify KYC documents → **Verify KYC** or **Reject KYC** | **Decision:** KYC Valid or Reject | Stage 1 |
| 3.3 | Grantor Validation | AMS Portal | **Credit Manager (CM)** | Review KYC-verified application → Verify employee/company info, salary → **Verify Grantor** or **Reject Grantor** | **Decision:** Grantor Valid or Reject | Stage 2 |
| 3.4 | HR Employee Verification | HR Portal | **Company HR Manager** | Login to HR Portal → Review application (own company only) → **Verify** or **Reject** employee | **Decision:** HR Verify or Reject | Stage 3 (NEW in 2.0) |
| 3.5 | Final Assessment | AMS Portal | **R&C (Risk & Compliance)** | Review fully verified application → Assess overall KYC legitimacy → **Approve** or **Decline** | **Decision:** Final Approve or Decline | Stage 4 |
| 3.6 | Employee Notification | Subscriber App | System (Auto) | Send approval/rejection notification to employee app + SMS | — | Auto |

### Phase 4 — EWA Amount Application & Disbursement

| Step | Activity | Channel/System | Owner/Actor | What Needs to Be Done | Decision Point | Status/Timeline |
|------|----------|----------------|-------------|----------------------|----------------|-----------------|
| 4.1 | Employee Info Update on Corporate Portal | AMS → Corporate Portal | MO Officer | Export approved list from AMS → Import department & employee data into Corporate Portal | — | Manual step |
| 4.2 | Employee Applies EWA Amount | Subscriber App | Employee | Select Kyo Htote → View Available Amount (50% of salary) → Key in amount → Select Wallet → Review fee → Sign agreement → Confirm with PIN | — | Monthly cycle (15th-24th) |
| 4.3 | Wallet Disbursement | System | System (Auto) | Amount credited to MoMoney wallet (minus 2.5% service fee). GL: Debit customer → Suspense GL → Credit customer (net) + Fee Income | — | Auto |
| 4.4 | OTC Disbursement (if non-wallet) | Agent App | Agent | Cash code sent via SMS → Employee withdraws full amount at agent shop (no partial withdrawal for OTC) | **Decision:** Wallet vs OTC | Per employee type |

### Phase 5 — Repayment

| Step | Activity                            | Channel/System     | Owner/Actor                                  | What Needs to Be Done                                                                       | Decision Point                          | Status/Timeline       |
| ---- | ----------------------------------- | ------------------ | -------------------------------------------- | ------------------------------------------------------------------------------------------- | --------------------------------------- | --------------------- |
| 5.1  | Corporate Portal Repayment          | Corporate Portal   | MO Officer / Corporate HR                    | Login → Advance Salary → Repayment → Select pending → Submit (partial or full)              | —                                       | Monthly               |
| 5.2  | DW Portal Repayment (Maker-Checker) | DW Portal          | **Maker** (submits) + **Checker** (approves) | Maker: New Request → EWA Repayment → Select Company → Submit; Checker: Verify → Approve     | **Decision:** Checker Approve/Reject    | Two-step verification |
| 5.3  | Payroll Auto Repayment              | Corporate Portal   | System (Auto)                                | Upload payroll file → System calculates Net Salary = Basic Salary - EWA amount → Auto repay | —                                       | Automated             |
| 5.4  | Late Fees Calculation               | System / HR Portal | System (Auto) / HR                           | Day 1-10: 0.1%/day; Day 11+: 0.2%/day (CR updated to 0.15%/day flat rate)                   | **Decision:** Late fee rate change (CR) | Auto + HR visibility  |
| 5.5  | Repayment Alerts                    | System             | System (Auto)                                | 3 days before due: reminder email; 1 day after due: overdue alert; Daily at 9AM MMT         | —                                       | Automated             |

---

## 📊 EWA 2.0 Project Events & Key Decisions Timeline

| Date       | Event/Decision                    | Owner                               | Details                                                                 |
| ---------- | --------------------------------- | ----------------------------------- | ----------------------------------------------------------------------- |
| 2025-02-18 | BRD v1.0 Created                  | Mai Khin Moe Moe Myat, Su Su Myaing | Initial EWA 2025 Individual BRD                                         |
| 2025-04-08 | BRD v2.0 Updated                  | Mai Khin Moe Moe Myat               | HR Portal section added                                                 |
| 2025-05-21 | BE & FE Dev Done                  | JITS / Dev Team                     | Backend + Frontend development completed                                |
| 2025-05-21 | Mobile API Integration Done       | Dev Team                            | Mobile app API integration & UI completed                               |
| 2025-06-04 | Email Integration WIP             | Dev Team                            | Email integration in progress                                           |
| 2025-06-11 | **Decision: CRs needed**          | Chief Sale                          | EWA 2.0 requires Change Requests → UAT blocked                          |
| 2025-06-17 | **EWA 2.0 Biz Final Meeting**     | Business Team                       | 18 discussion points identified to fix/CR/develop                       |
| 2025-06-19 | BRD v3.0 Updated                  | Mai Khin Moe Moe Myat               | Guarantor, calculation changes (purple highlight)                       |
| 2025-06-23 | MoBiz-HR Integration BRD          | Mai Khin Moe Moe Myat               | New BRD for Integration between MoBiz EWA and HR Portal                 |
| 2025-06-25 | MoBiz-AMS CR Raised to JITS       | IT Team                             | CR for AMS changes submitted                                            |
| 2025-06-30 | Dev Target Date                   | Dev Team                            | Tentative dev completion for 18 discussion points                       |
| 2025-07-02 | **Decision: Direct DB Access**    | JITS                                | JITS will access MoBiz DB via AMS for repayment/loan data visualization |
| 2025-08-07 | Release Production Folder Created | Release Team                        | "Release Production - EWA 2.0 (KyoHtote)" folder created                |
| 2025-08-12 | Late Fees CR Created              | Mai Khin Moe Moe Myat               | CR: Change late fee from slab (0.1%/0.2%) to flat 0.15%/day             |
| 2025-09-02 | CR for EWA Features (HR/AMS)      | Thảo Bùi                            | Change Request for EWA related features on HR Portal & AMS              |
| 2025-09-22 | CR for EWA Features (MoBiz)       | Thảo Bùi                            | JITS CR for MoBiz Portal changes                                        |

---

## 👥 RACI-Style Responsibility Matrix

| Role | Person/Team | Responsibilities |
|------|------------|------------------|
| **Product Manager** | Mai Khin Moe Moe Myat | BRD authoring (Sub, MoBiz, HR Portal), document updates, CR management |
| **Product Owner (AMS)** | Su Su Myaing | AMS Portal requirements, KYC workflow design |
| **Head of IT / Approver** | Kyaw Sun Linn | BRD approval, CR approval, technical decisions |
| **CIO / Approver** | Kimy Loca | High-level approval for integrations |
| **JITS Dev Team** | JITS Innovation Labs | Backend/Frontend development, CR implementation, DB access |
| **MO Officer / Operation** | Operation Team | Corporate setup, employee import, EWA config, repayment processing |
| **Operation Manager (OM)** | OM Team | KYC validation (Stage 1) on AMS |
| **Credit Manager (CM)** | Credit Team | Grantor validation (Stage 2) on AMS |
| **Company HR** | External HR | Employee verification (Stage 3) on HR Portal |
| **R&C (Risk & Compliance)** | R&C Team | Final EWA approval/decline (Stage 4) on AMS |
| **Maker/Checker** | Operation Supervisor | Repayment processing (two-step verification) on DW Portal |
| **MO Sale Team** | Sales | Record company list, create HR accounts on HR Portal |
| **QA** | Nyein Chan Aung, Bhone Nanda Kyaw | Testing and UAT |

---

## ⚠️ Current Status (as of latest updates)

- **Development:** BE/FE done, but **18 CRs identified** from the June 17 business meeting are being addressed
- **UAT:** Blocked pending CR resolution; target dev completion was June 30, 2025
- **Production Release:** Folder created (Aug 2025) — release pending CR completion
- **New Platform Proposal:** JITS submitted a full EWA Platform Implementation proposal (WageFlow v4) — 4-month delivery, USD 32,000, with new standalone architecture

---

**Key Sources:**
- [EWA (Kyo Htote) Individual 2025 Version](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1326055425/EWA+Kyo+Htote+Individual+2025+Version)
- [EWA Comprehensive Summary Document](https://team-1607663466339.atlassian.net/wiki/spaces/~712020f2b5537313354b4fa9732498d150e182/pages/1901166594/EWA+Earned+Wage+Access+Comprehensive+Summary+Document)
- [JITS Proposal for EWA Platform](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1906769922/JITS+Innovation+Labs+Proposal+for+EWA+Platform+Implementation)
- [EWA MVPs Roadmap](https://team-1607663466339.atlassian.net/wiki/spaces/TEC/pages/726270138/EWA+MVPs+Roadmap)
- [Integration between MoBiz EWA and HR Portal](https://team-1607663466339.atlassian.net/wiki/spaces/MOAG/pages/1461059587/Integration+between+MoBiz+EWA+and+HR+Portal)
