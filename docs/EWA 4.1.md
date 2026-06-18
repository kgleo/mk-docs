

# EWA Platform — Camunda BPMN 2.0 Master Execution Flow Specification

## 1. System Architecture & BPMN Scope

The EWA workflow is implemented as a Camunda BPMN 2.0 process with multiple separated Pools representing independent business systems. Each Pool communicates through BPMN Message Events to maintain loose coupling between the customer application, EWA core engine, employer HR system, risk operations, and payment infrastructure.

The workflow follows an asynchronous orchestration model where the EWA Platform acts as the central process engine controlling validation, verification, approval, contract generation, and payout execution.

The primary relationship mapping uses:

```
employee_id + corporate_id
```

This composite key ensures that every EWA request is evaluated against the correct employee-employer relationship.

|Pool|Purpose|Main Responsibility|
|---|---|---|
|Customer Application Pool|Employee journey|Registration, KYC, request submission|
|EWA Core Platform Pool|Process controller|BPMN execution, validation, calculation|
|Corporate HR Portal Pool|Employer verification|Employee and payroll confirmation|
|Risk Backoffice Pool|Manual decision|Compliance and approval|
|Payment Engine Pool|Financial execution|Wallet transfer and retry handling|

---

# 2. Customer Intake & Application Creation

The lifecycle starts when an employee initiates an EWA request from the mobile application. The customer side collects required information and prepares a secure request package.

Before sending the request, the application validates whether the employee belongs to an active registered company. If the company is not available, the process does not terminate; instead, it enters a waiting state until the company onboarding process is completed.

After submission, the application generates an idempotency key to prevent duplicate loan requests caused by network retry or repeated user actions.

|Step|BPMN Object|Description|Result|
|---|---|---|---|
|1|Start Event|Employee opens EWA service|New process instance|
|2|User Task T1|Employee completes registration|Customer profile created|
|3|User Task T2|Upload KYC and employment details|Verification data collected|
|4|Gateway G1|Check company registration|Continue or wait|
|5|Task T4|Submit company lead|Company onboarding request|
|6|Event E1|Wait for company activation|Process resumes|
|7|Task T3|Enter requested withdrawal amount|Loan request prepared|
|8|Message M1|Send request to platform|EWA processing starts|

---

# 3. EWA Platform Processing & Employer Verification

Once the EWA engine receives the application, Camunda creates the workflow instance and begins backend processing.

The platform first performs technical validation, including idempotency checking, transaction duplication prevention, and data integrity verification.

A transaction record is created before any external verification happens. This guarantees that every request has an audit trail even if later steps fail.

The platform then checks employer availability and calculates the available wage access amount using the employee and corporate relationship mapping.

|Step|BPMN Object|Description|Output|
|---|---|---|---|
|1|Message Event M2|Receive customer request|Application captured|
|2|Service Task T5|Create ledger record|EWA transaction created|
|3|Gateway G2|Check company status|Active / Pending|
|4|Task T7|Mark company pending|COMPANY_PENDING|
|5|Timer Event E2|Wait for activation|Resume later|
|6|Service Task T6|Calculate EWA limit|Available amount|
|7|Task T8|Prepare HR package|Verification request|
|8|Message M3|Send HR request|HR review starts|

---

# 4. Corporate HR Verification & Risk Assessment

The employer verification process runs in a separate HR Portal Pool.

The HR system receives the verification message and creates a review case. An HR officer validates employee employment status, payroll information, and eligibility.
![[ChatGPT Image Jun 13, 2026, 07_03_41 PM 1.png]]
If the submitted information is incorrect, the 
![[diagram (10).svg]]process enters a correction loop. The customer updates missing or incorrect data and the workflow returns to verification.
![[diagram (7).svg]]

After HR confirmation, the request moves into the risk review stage.![[ChatGPT Image Jun 13, 2026, 06_14_09 PM.png]]

| Step | BPMN Object  | Description              | Process Result         |
| ---- | ------------ | ------------------------ | ---------------------- |
| 1    | Message M4   | HR receives request      | HR case created        |
| 2    | User Task T9 | HR reviews employee data | Validation completed   |
| 3    | Gateway G3   | Information correct?     | Approve or correction  |
| 4    | Task T11     | Request correction       | Return loop            |
| 5    | Task T13     | Customer updates data    | Re-submit              |
| 6    | Task T10     | HR approves              | Confirmation generated |
| 7    | Message M5   | Return HR result         | Risk process begins    |
| 8    | Message M7   | Send risk case           | Backoffice review      |

Risk officers evaluate the request based on business rules, compliance requirements, and exposure limits.

| Decision         | Action                       |
| ---------------- | ---------------------------- |
| Approved         | Generate authorization       |
| Reject           | Mark application failed      |
| More Information | Request additional documents |

---

# 5. Contract Generation & Payment Execution

After risk approval, the EWA platform finalizes the loan agreement and prepares the payout instruction.

The contract generation step creates the legal agreement record and links it to the original application and employee-corporate relationship.

The payment engine operates independently and only receives payout instructions through BPMN messaging.

|Step|BPMN Object|Description|Result|
|---|---|---|---|
|1|Message M9|Receive approval|Continue payout|
|2|Gateway G6|Validate approval|Contract flow|
|3|Service Task T18|Generate agreement|Digital contract created|
|4|Service Task T20|Create payout request|Payment instruction|
|5|Message M10|Send payment request|Wallet transfer starts|
|6|Service Task T21|Execute transfer|Success / Failure|
|7|Gateway G7|Payment status check|Complete or retry|

If payment succeeds, the loan becomes active.

```
ONGOING
```

If payment fails, the system executes retry logic.

---

# 6. Failure Handling, Status Management & Vendor Rules

The BPMN implementation must support recovery scenarios because financial transactions cannot rely only on a single execution attempt.

Each process instance must maintain complete state tracking, message correlation, and audit history.

Required technical fields:

|Field|Purpose|
|---|---|
|employee_id|Employee ownership|
|corporate_id|Employer relationship|
|application_id|Workflow reference|
|transaction_id|Financial tracking|
|idempotency_key|Duplicate prevention|

Transaction lifecycle:

| Status          | Meaning                     |
| --------------- | --------------------------- |
| CREATED         | Application created         |
| COMPANY_PENDING | Waiting employer activation |
| HR_PENDING      | Waiting HR confirmation     |
| RISK_PENDING    | Under review                |
| APPROVED        | Approved for payout         |
| ONGOING         | Active loan                 |
| FAILED          | Rejected or failed          |
| COMPLETED       | Closed lifecycle            |

Vendor implementation requirements:

- Use BPMN 2.0 Pools and Message Flows.
    
- uUse Service Tasks for automated operations.
    
- Use User Tasks for human review.
    
- Use Exclusive Gateways for decisions.
    
- Use Timer Events for waiting states.
    
- Maintain full audit trail.
    
- Support retry, rollback, and recovery.
    

This document acts as the implementation reference for building the EWA workflow engine in Camunda BPMN 2.0.