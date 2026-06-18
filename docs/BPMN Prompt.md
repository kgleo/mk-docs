𝐦 𝐏𝐫𝐨𝐦𝐩𝐭 → 𝐁𝐏𝐌𝐍 𝟐.𝟎 𝐃𝐢𝐚𝐠𝐫𝐚𝐦 (𝐢𝐧 𝐦𝐢𝐧𝐮𝐭𝐞𝐬)

Most Business Analysts don’t realize this: you can ask AI to generate BPMN 2.0 XML directly. Paste that XML into Camunda Modeler and—boom—you’ve got a clean, editable process diagram.

Here’s the playbook I use:

1) Describe your process like a BA (not like a developer)
Start with roles, steps, decisions, outcomes, and exceptions.
Example process (simple & real): “Leave Request Approval”
Employee submits leave request
Manager reviews
If approved → notify employee → end
If not approved → send back for revision → resubmit → review again

2) Prompt AI to create BPMN 2.0 XML (with DI)
Ask for:
Valid BPMN 2.0 XML (with namespaces)
isExecutable="true"
BPMN DI (so Camunda can render layout)
Clear IDs, sequence flows, and gateway conditions

𝐒𝐚𝐦𝐩𝐥𝐞 𝐩𝐫𝐨𝐦𝐩𝐭 𝐲𝐨𝐮 𝐜𝐚𝐧 𝐫𝐞𝐮𝐬𝐞:

You are a BPMN 2.0 expert. Generate a complete, valid BPMN 2.0 XML with BPMN DI for Camunda Modeler. 
Model this process:
- Start → User Task: Submit Leave Request (assignee = ${employeeId})
- User Task: Manager Review (assignee = ${managerId})
- Exclusive Gateway: Approved?
  - Yes → End: Approved
  - No → User Task: Revise Request (assignee = ${employeeId}) → back to Manager Review
Use sensible layout coordinates, unique IDs, isExecutable="true", and add conditionExpressions:
- Yes flow: ${approved}
- No flow: ${!approved}
Return only the XML.

3) Get it into Camunda Modeler (two easy ways)
Option A (safest):
Copy the XML into a text editor
Save as leave-approval.bpmn (UTF-8)
Open Camunda Modeler → File → Open Diagram and select the file
Option B (direct paste, if your version supports it):
New BPMN Diagram in Camunda Modeler
Switch to the XML view (top/right)
Replace all with your XML → save → switch back to Diagram

4) Iterate like a BA
Rename tasks for business clarity
Add lanes/pools for roles (Employee, Manager, HR)
Attach boundary events (e.g., timer for SLA)
Add data objects or messages if you hand off to another system

5) Common gotchas (and fixes)
Blank canvas? Your XML probably lacks BPMN DI (shapes/edges). Ask AI to include bpmndi with coordinates.
Validation errors? Ensure namespaces are present and IDs are unique.
Camunda extensions? Use camunda: attributes only when needed (e.g., camunda:assignee, camunda:formKey).
Gateway conditions must use conditionExpression and xsi:type="bpmn:tFormalExpression".

𝐇𝐨𝐰 𝐈 𝐬𝐜𝐚𝐥𝐞 𝐭𝐡𝐢𝐬 𝐟𝐨𝐫 𝐫𝐞𝐚𝐥 𝐩𝐫𝐨𝐣𝐞𝐜𝐭𝐬?
Add pools/lanes (e.g., Employee, Manager, HR, Payroll) and message flows between systems
Model sub-processes (e.g., “Escalation”) with boundary timer events for SLAs
Attach data objects (e.g., LeaveRequest, Policy) to clarify inputs/outputs
Keep IDs stable; map them to user stories and test cases for traceability