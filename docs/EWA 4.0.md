Below is the **BPMN 2.0 Diagram Specification Table** for the EWA / Salary Advance Loan Lifecycle. This table can be used as a **BPMN design document** before creating BPMN XML (Camunda, Flowable, Bizagi, Signavio).

## 1. BPMN Pool & Lane Structure

|Pool|Lane|Responsibility|System / Actor|
|---|---|---|---|
|Customer|Employee|Submit loan request, provide KYC, update data|Mobile App / Web|
|EWA Platform|Application Intake|Receive application, create loan case|EWA Core System|
|EWA Platform|Verification & Limit|Calculate eligibility, manage HR verification|EWA Core System|
|Company HR Portal|HR Officer|Verify employment information|Employer System|
|EWA Platform|Decision & Contract|Process approval, create contract|EWA Core System|
|Backoffice|Risk Team|Review risk and approve/reject|Admin Portal|
|Payment Engine|Wallet Transfer|Execute salary advance payout|Payment Service|
|EWA Platform|Loan Management|Track repayment lifecycle|EWA Core System|
|Global Service|Audit|Record all activities|Audit Log Service|

---

# 2. BPMN Element Inventory

|ID|BPMN Element|Name|Pool / Lane|Description|
|---|---|---|---|---|
|S1|Start Event|Customer Registration Start|Customer|User starts onboarding|
|T1|User Task|Register Account|Customer|Create account|
|T2|User Task|Submit KYC & Employment Data|Customer|Upload required data|
|G1|Exclusive Gateway|Company Registered?|Customer|Check employer availability|
|T3|User Task|Create EWA Request|Customer|Request salary advance|
|T4|User Task|Submit Company Lead|Customer|Add missing employer|
|E1|Intermediate Timer Event|Wait Company Setup|Customer|Wait employer activation|
|M1|Message Flow|Send Loan Application|Customer → EWA|Submit application|

---

# 3. EWA Application Processing

| ID  | BPMN Element        | Name                           | Type    | Logic                      |
| --- | ------------------- | ------------------------------ | ------- | -------------------------- |
| M2  | Start Message Event | Receive Loan Application       | Event   | Receive customer request   |
| T5  | Service Task        | Create Loan Application Record | Task    | Store application          |
| T6  | Service Task        | Calculate Estimated Limit      | Task    | Calculate available amount |
| G2  | Exclusive Gateway   | Company Active?                | Gateway | Check company status       |
| T7  | Service Task        | Set Status COMPANY_PENDING     | Task    | Pause application          |
| E2  | Intermediate Event  | Wait Company Activation        | Event   | Waiting state              |
| T8  | Service Task        | Send HR Verification Request   | Task    | Request employment check   |
| M3  | Message Flow        | Send Verification Message      | Message | Platform → HR              |

---

# 4. HR Verification Process

|ID|BPMN Element|Name|Lane|Description|
|---|---|---|---|---|
|M4|Start Message Event|Receive Verification Request|HR Portal|Receive employee check|
|T9|User Task|Review Employee Information|HR|Validate employee|
|G3|Exclusive Gateway|Information Correct?|HR|Decision point|
|T10|User Task|Approve Employment|HR|Confirm employee|
|T11|User Task|Request Update|HR|Reject / ask correction|
|M5|Message Flow|HR Response|HR → EWA|Return result|

---

# 5. Limit Finalization

|ID|BPMN Element|Name|Logic|
|---|---|---|---|
|M6|Intermediate Message Event|Receive HR Response|Receive approval|
|G4|Exclusive Gateway|HR Result|Approved / Update|
|T12|Service Task|Recalculate Final Limit|Calculate final amount|
|T13|User Notification Task|Notify Customer|Request update|
|LOOP1|Loop Flow|Customer Update Data|Repeat correction|

---

# 6. Risk Review Process

|ID|BPMN Element|Name|Description|
|---|---|---|---|
|M7|Message Event|Send Risk Review Request|Create review case|
|M8|Start Message Event|Receive Review Task|Risk team receives|
|T14|User Task|Risk Assessment|Manual review|
|G5|Exclusive Gateway|Risk Decision|Approve / Reject / More Info|
|T15|User Task|Approve Loan|Approve|
|T16|User Task|Reject Loan|Decline|
|T17|User Task|Request Data|Need additional info|

---

# 7. Contract & Payout

|ID|BPMN Element|Name|Description|
|---|---|---|---|
|M9|Message Event|Receive Risk Result|Result received|
|G6|Exclusive Gateway|Approved?|Decision|
|T18|Service Task|Create Loan Contract|Generate agreement|
|T19|Service Task|Close Application|End rejected case|
|T20|Service Task|Send Payout Request|Request payment|

---

# 8. Payment Engine Flow

|ID|BPMN Element|Name|BPMN Type|
|---|---|---|---|
|M10|Start Message Event|Receive Payout Request|Event|
|T21|Service Task|Execute Wallet Transfer|Task|
|G7|Exclusive Gateway|Payment Success?|Decision|
|T22|Service Task|Activate Loan|Success|
|T23|Service Task|Retry Payment|Failure handling|
|G8|Exclusive Gateway|Retry Limit?|Decision|
|T24|Service Task|Reverse Transaction|Final failure|
|E3|End Event|Payment Failed End|Termination|

---

# 9. Loan Management Lifecycle

|ID|BPMN Element|Name|Description|
|---|---|---|---|
|T25|Service Task|Monitor Due Date|Schedule tracking|
|T26|Service Task|Send Reminder|Notification|
|G9|Exclusive Gateway|Repayment Status|Check payment|
|T27|Task|Close Loan|Paid|
|T28|Task|Continue Schedule|Partial payment|
|T29|Task|Collection Process|Overdue|

---

# 10. BPMN Gateway Rules

|Gateway|Type|Condition|
|---|---|---|
|Company Registered?|XOR|Yes / No|
|Company Active?|XOR|Active / Pending|
|Information Correct?|XOR|Correct / Update|
|HR Result|XOR|Approve / Update|
|Risk Decision|XOR|Approve / Reject / More Info|
|Approved?|XOR|Continue / Close|
|Payment Success?|XOR|Success / Retry|
|Retry Limit?|XOR|Retry / Reverse|
|Repayment Status?|XOR|Paid / Partial / Overdue|

---

# 11. BPMN Message Flow Map

|From|To|Message|
|---|---|---|
|Customer|EWA Platform|Loan Application|
|EWA Platform|HR Portal|Verification Request|
|HR Portal|EWA Platform|Employment Result|
|EWA Platform|Risk Team|Risk Review|
|Risk Team|EWA Platform|Approval Result|
|EWA Platform|Payment Engine|Payout Request|
|Payment Engine|EWA Platform|Payment Status|

---

# 12. BPMN 2.0 XML Mapping Ready

|BPMN Concept|XML Tag|
|---|---|
|Pool|`<bpmn:participant>`|
|Lane|`<bpmn:lane>`|
|Task|`<bpmn:task>`|
|Service Task|`<bpmn:serviceTask>`|
|User Task|`<bpmn:userTask>`|
|Gateway|`<bpmn:exclusiveGateway>`|
|Parallel Gateway|`<bpmn:parallelGateway>`|
|Start Event|`<bpmn:startEvent>`|
|End Event|`<bpmn:endEvent>`|
|Message Event|`<bpmn:messageEventDefinition>`|
|Flow|`<bpmn:sequenceFlow>`|

This table is the **BPMN implementation blueprint** for the EWA process.