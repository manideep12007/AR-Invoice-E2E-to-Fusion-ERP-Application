📑 AR Invoice Application – End-to-End Flow (VBCS → OPA → OIC → ERP)

📌 Overview

This project demonstrates an end-to-end Oracle Cloud solution for creating, approving, and submitting Accounts Receivable (AR) Invoices into ERP Cloud.
It showcases how multiple Oracle technologies integrate together:

VBCS (Visual Builder Cloud Service): Used as the UI layer to capture invoice details.
OPA/PCS (Oracle Process Automation): Handles approval workflow with status updates and conditional routing.
OIC (Oracle Integration Cloud): Orchestrates data flow between VBCS, PCS, and ERP.
ERP Cloud (Fusion): Final system of record where the invoice is created.

🏗️ Business Process Flow

1.Invoice Creation
User logs into VBCS and enters invoice details (Customer, Amount, Due Date, etc.).
2.Approval Workflow
The invoice is submitted for approval in PCS.
Roles: Requestor, Manager and Finance Director 
3.Integration to ERP
Upon approval, OIC integration is triggered.
OIC triggers ERP connection in integration and 
fetches Invoice and lines details then sends to ERP cloud
4.ERP Invoice Creation
ERP Cloud receives the payload and creates the invoice.
ERP responds with an Invoice ID and status.
5.Confirmation to User
OIC sends the confirmation back to VBCS, displaying the ERP Invoice ID to the user.
Is either Completed or Errored

🏗️ Business Process Flow

1. Invoice Creation (VBCS)
End user (Requestor) logs into the VBCS AR Invoice App and enters invoice details (Customer, Product, Amount, Due Date, etc.).
Once submitted, the invoice status = Submitted.

2. Approval Workflow (PCS)
The invoice is routed to Manager for first-level approval.
If the amount ≤ 1000 USD, only Manager approval is required.
If the amount > 1000 USD, it conditionally routes to the Finance Director for second-level approval.
Based on workflow outcome:
If approved → invoice status = Approved.
If rejected → invoice status = Rejected (and the process ends).

3. Integration to ERP (OIC)
When the invoice is Approved, OIC is triggered.
OIC integration fetches the Invoice Header and Line details from VBCS.
It transforms and sends the payload to ERP Cloud using the ERP Cloud Adapter.

4. ERP Invoice Creation
ERP Cloud receives the payload and creates the invoice.
ERP responds with:
Transaction Number
Status (SUCCESS/ERROR)

5. Confirmation to User (VBCS)
If ERP creation is successful → OIC updates invoice status = Completed in VBCS and displays the ERP Transaction Number to the user.
If ERP creation fails → OIC updates invoice status = Error in VBCS with the error message

📂 Components
1. VBCS
Web Application: AR Invoice App
Business Objects:
Customer → one customer can have multiple invoices
Product → one product can be used in multiple invoice lines
Invoice → one invoice can have multiple invoice lines
InvoiceLine → stores individual line-level details
Service Connections: PCS URL, OIC REST endpoints

2. PCS / OPA
Workflow Stages:
Approver 1: Manager
Approver 2: Finance Director (conditional: invoice amount > 1000)
Business Rules: Implemented using PCS Rule Designer
Outcomes: Updates invoice status to Approved/Rejected

3. OIC Integrations
Integration Type: App-driven
Connections:
VBCS REST Adapter
ERP Cloud Adapter
Process:
Triggered when invoice = Approved.
Fetch invoice header + line details from VBCS.
Transform data into ERP format.
Submit to ERP Invoice Service.
Update VBCS with ERP transaction response.
Error Handling:
Fault policies
Tracking ID logging
Updates status in VBCS as “Error”

🔍 Example Status Transitions
Requestor submits invoice → Submitted
Manager approves → Approved (or if rejected → Rejected)
Finance Director approves (if applicable) → Approved
OIC triggers ERP submission:
If ERP success → Completed (with Transaction Number)
If ERP failure → Error

