# BPMN 2.0 Enterprise Designer Skill

## Purpose

Generate BPMN 2.0 process models and Camunda-compatible importable BPMN XML from business process descriptions.

The generated BPMN must be:

- BPMN 2.0 compliant
- Camunda compatible
- Importable into Camunda Modeler
- Importable into bpmn.io
- Include BPMNDI coordinates
- Include sequence flows
- Include gateways
- Include events
- Include lane definitions when actors exist
- XML schema valid

---

# INPUT

User may provide:

- Business Process Flow
- User Story
- SOP
- BRD
- PRD
- FSD
- Workflow Description
- Event Flow
- State Flow

Example:

Employee Request EWA

Check Employee Status

If Eligible Calculate Available Amount Create Advance Request Transfer Money Else Reject Request

---

# STEP 1 — PROCESS DISCOVERY

Extract:

## Actors

Examples:

- Employee
- HR
- Company
- Wallet Service
- Loan Service
- Payout Service

## Activities

Examples:

- Check Eligibility
- Calculate Amount
- Create Request
- Transfer Money

## Decisions

Examples:

- Eligible?
- Sufficient Balance?
- KYC Approved?

## Events

Examples:

- Request Submitted
- Money Transferred
- Loan Created

## Data Objects

Examples:

- Employee
- Wallet
- Loan
- Transaction

---

# STEP 2 — BPMN MAPPING RULES

## Start Event

Create exactly one Start Event unless specified.

Map:

Request Received Customer Apply Employee Submit

to

Start Event

---

## End Event

Create End Event for:

Success Path Failure Path

Examples:

Approved Rejected Completed Cancelled

---

## Service Task

Use for system execution.

Examples:

Calculate Amount Call API Transfer Money Post GL Create Transaction

---

## User Task

Use for human action.

Examples:

Approve Request Review Application Verify KYC

---

## Exclusive Gateway

Use when:

Yes / No Approved / Rejected

Examples:

Eligible? Balance Available?

---

## Parallel Gateway

Use when multiple tasks run simultaneously.

Examples:

Check KYC Check Fraud Check Risk

---

## Event-Based Gateway

Use when process waits for external events.

Examples:

Callback Received Payment Confirmed

---

# STEP 3 — LANE GENERATION

Create lanes from actors.

Example:

Pool: EWA Process

Lane: Employee

Lane: HR

Lane: Wallet Service

Lane: Payout Service

---

# STEP 4 — BPMN VALIDATION RULES

Validate:

- Start Event exists
- End Event exists
- No orphan task
- Gateway has incoming/outgoing flow
- Sequence Flow IDs unique
- Element IDs unique
- Lane references valid
- Pool references valid

Reject invalid BPMN.

---

# STEP 5 — XML GENERATION RULES

Must generate:

definitions

process

startEvent

endEvent

task

userTask

serviceTask

exclusiveGateway

parallelGateway

sequenceFlow

laneSet

lane

BPMNDiagram

BPMNPlane

BPMNShape

BPMNEdge

bounds

waypoint

---

# STEP 6 — CAMUNDA COMPATIBILITY

Namespaces required:

xmlns:bpmn xmlns:bpmndi xmlns:dc xmlns:di xmlns:xsi

Generate:

isExecutable="true"

for executable processes.

---

# STEP 7 — BPMNDI REQUIREMENTS

Every BPMN element must contain:

BPMNShape

Every flow must contain:

BPMNEdge

Every shape must contain:

dc:Bounds

Every edge must contain:

di:waypoint

Never generate BPMN without BPMNDI.

---

# STEP 8 — ERROR DETECTION

Detect:

Missing Start Event

Missing End Event

Broken Sequence Flow

Disconnected Gateway

Duplicate IDs

Missing BPMNDI

Invalid Namespace

Repair automatically.

---

# STEP 9 — OUTPUTS

Generate:

1. BPMN Process Summary
    
2. BPMN Element Inventory
    

|Type|Count|
|---|---|
|Start Event|X|
|User Task|X|
|Service Task|X|
|Gateway|X|
|End Event|X|

3. BPMN XML
    
4. Validation Result
    
5. Camunda Import Readiness
    

PASS / FAIL

---

# FINTECH DOMAIN PATTERNS

## EWA

Start ↓ Check Employee ↓ Eligibility Gateway ↓ Calculate Amount ↓ Create Advance ↓ Transfer Money ↓ End

---

## Wallet Transfer

Start ↓ Validate Wallet ↓ Balance Check ↓ Debit Wallet ↓ Credit Wallet ↓ Post GL ↓ Publish Event ↓ End

---

## Loan Disbursement

Start ↓ KYC Check ↓ Risk Check ↓ Approval Gateway ↓ Create Loan ↓ Disburse Money ↓ End

---

## Payout Channel

Start ↓ Validate Request ↓ Route Channel ↓ Fee Calculation ↓ Transfer ↓ Callback Wait ↓ Update Status ↓ End

---

# STRICT RULES

Never skip BPMNDI.

Never generate incomplete XML.

Never generate pseudo XML.

Never omit namespaces.

Never omit sequenceFlow.

Always produce importable BPMN.

Target compatibility:

- Camunda Modeler
- Camunda 8
- bpmn.io
- Flowable
- Activiti
- Zeebe