## Use Cases

---

### UC-001: Register Product Warranty Online

- **Related Requirement:** FR-001
- **Actor:** Customer
- **Precondition:** Customer has purchased a product and has access to the internet via a browser or mobile device
- **Main Flow:**
  1. Customer navigates to the warranty registration portal
  2. Customer creates an account or logs into an existing account
  3. Customer selects "Register a New Product" and enters the required product and purchase details
  4. Customer reviews the entered details on a summary screen and confirms submission
  5. The system processes the registration and displays a confirmation screen with the warranty details
- **Alternative Flow:** Customer does not have internet access and contacts Customer Service. A Customer Service Representative completes the registration on the customer's behalf through the internal portal (see UC-030)
- **Postcondition:** The warranty registration is recorded in the system, a unique reference number is assigned, and the customer receives a confirmation email

---

### UC-002: Enforce Mandatory Fields During Warranty Registration

- **Related Requirement:** FR-002
- **Actor:** Customer
- **Precondition:** Customer is on the warranty registration form in the customer portal
- **Main Flow:**
  1. Customer begins filling in the registration form, entering product model, serial number, purchase date, retailer name, and contact details
  2. Customer completes all mandatory fields correctly
  3. Customer clicks Submit
  4. The system validates that all mandatory fields are present and correctly formatted
  5. The system accepts the submission and proceeds to registration confirmation
- **Alternative Flow:** Customer leaves one or more mandatory fields blank or enters data in an invalid format and clicks Submit. The system highlights each incomplete or invalid field with a clear inline error message in plain language. The form is not submitted until all mandatory fields are correctly completed. The customer corrects the fields and resubmits
- **Postcondition:** The registration record in the system contains a complete set of mandatory data with no null values in required fields

---

### UC-003: Access Registration Portal via QR Code

- **Related Requirement:** FR-003
- **Actor:** Customer
- **Precondition:** Customer has purchased a product and has product packaging containing a printed QR code; customer has a smartphone with a camera
- **Main Flow:**
  1. Customer opens the product packaging and locates the QR code printed on the insert or packaging material
  2. Customer scans the QR code using their smartphone camera or a QR reader app
  3. The system directs the customer to the warranty registration portal, with the product model pre-populated where possible
  4. Customer logs in or creates an account
  5. Customer completes and submits the abbreviated registration form
- **Alternative Flow:** The QR code is damaged or cannot be scanned. The customer navigates manually to the warranty registration portal URL printed alongside the QR code and completes registration without pre-populated product details
- **Postcondition:** Customer reaches the registration portal with minimal navigation steps and successfully submits their registration

---

### UC-004: Calculate and Store Warranty Expiry Date

- **Related Requirement:** FR-004
- **Actor:** System (automated, triggered at registration submission)
- **Precondition:** A warranty registration has been submitted with a valid product model and purchase date; the product catalogue contains a warranty duration for the submitted product model
- **Main Flow:**
  1. Customer submits a completed warranty registration form including purchase date and product model
  2. The system retrieves the warranty duration for the product model from the product catalogue
  3. The system calculates the warranty expiry date by adding the warranty duration to the purchase date
  4. The system stores the calculated expiry date on the warranty registration record
  5. The expiry date is displayed to the customer on the confirmation screen and included in the confirmation email
- **Alternative Flow:** The product model submitted does not have a warranty duration configured in the product catalogue. The system flags the registration record as requiring manual review and notifies the System Administrator. The expiry date field is left blank until the administrator resolves the product catalogue entry and manually updates the record
- **Postcondition:** The warranty registration record contains a calculated, stored expiry date that is visible to the customer and to internal users

---

### UC-005: Validate Serial Number Against Product Catalogue

- **Related Requirement:** FR-005
- **Actor:** System (automated, triggered at registration submission)
- **Precondition:** Customer has submitted a warranty registration form including a product serial number; the product catalogue is accessible to the system
- **Main Flow:**
  1. Customer submits the registration form with a product serial number entered
  2. The system queries the product catalogue to verify whether the serial number corresponds to a recognised product
  3. The serial number is found and matches a valid product record
  4. The system proceeds with registration using the verified product details
  5. Confirmation is displayed to the customer
- **Alternative Flow — Serial number not found:** The serial number does not match any record in the product catalogue. The system applies the configured business rule: either it rejects the registration with a clear message asking the customer to check the serial number and resubmit, or it accepts the registration provisionally and flags it with a manual review status for administrator investigation. The customer is informed of the outcome in either case
- **Postcondition:** The registration is either confirmed with a verified serial number, rejected with a clear explanation, or accepted provisionally and flagged for manual review

---

### UC-006: Detect and Prevent Duplicate Warranty Registration

- **Related Requirement:** FR-006
- **Actor:** Customer
- **Precondition:** A warranty registration already exists in the system for the same product serial number
- **Main Flow:**
  1. Customer navigates to the registration portal and begins the registration process
  2. Customer enters a product serial number and completes all other mandatory fields
  3. Customer submits the registration form
  4. The system checks the serial number against all existing registration records
  5. No existing registration is found for that serial number and the registration proceeds normally
- **Alternative Flow — Duplicate detected:** The system finds an existing active registration for the submitted serial number. The system prevents the duplicate registration from being created and displays a clear message informing the customer that this product is already registered. The system simultaneously creates an alert flag visible to the System Administrator showing the attempted duplicate registration, including the details of both the existing and attempted records
- **Postcondition:** No duplicate registration record exists in the system; the administrator is alerted to any duplicate attempt for investigation

---

### UC-007: Generate Warranty Registration Reference Number

- **Related Requirement:** FR-007
- **Actor:** System (automated, triggered at successful registration)
- **Precondition:** A warranty registration has been successfully validated and saved to the system
- **Main Flow:**
  1. Customer completes and submits the registration form
  2. The system validates and saves the registration record
  3. The system automatically generates a unique, non-sequential registration reference number
  4. The reference number is stored against the registration record
  5. The reference number is displayed on the registration confirmation screen immediately after submission
- **Alternative Flow:** The reference number generation fails due to a system error. The system displays a general error message to the customer advising them that registration could not be completed and asking them to try again. The partial registration is not saved. An error event is logged for administrator investigation
- **Postcondition:** The registration record has a unique reference number stored against it, and the customer has been shown that reference number on screen

---

### UC-008: Send Automated Registration Confirmation Email

- **Related Requirement:** FR-008
- **Actor:** System (automated, triggered at successful registration)
- **Precondition:** A warranty registration has been successfully completed and a customer email address is recorded on the registration
- **Main Flow:**
  1. The system confirms that a warranty registration has been saved successfully
  2. The system generates a confirmation email using the configured registration confirmation template
  3. The email is populated with the customer's name, warranty reference number, product details, purchase date, and calculated expiry date
  4. The system dispatches the email to the customer's registered email address
  5. The outbound notification is logged in the notification log with a timestamp and delivery status
- **Alternative Flow:** The email fails to deliver — for example, due to an invalid email address or a temporary email service outage. The system logs the failed delivery, retries up to the configured number of times, and if delivery continues to fail, surfaces the failure to the System Administrator for manual follow-up
- **Postcondition:** The customer has received a confirmation email containing all relevant warranty details; the notification event is recorded in the system log

---

### UC-009: Apply Versioned Warranty Terms to Registration

- **Related Requirement:** FR-009
- **Actor:** System (automated, triggered at registration)
- **Precondition:** The product catalogue contains versioned warranty terms records with effective dates; the customer's purchase date is captured during registration
- **Main Flow:**
  1. Customer submits a warranty registration with a product model and purchase date
  2. The system identifies the product in the product catalogue
  3. The system retrieves the version of the warranty terms that was in effect on the customer's purchase date
  4. The system links that specific terms version to the registration record
  5. The applicable terms version is stored on the registration and is used as the reference for all future claim assessments against that registration
- **Alternative Flow:** No warranty terms version exists in the catalogue for the date of purchase — for example, the product predates the system's historical records. The system flags the registration for manual review and notifies the administrator to assign the correct terms version before claim processing can proceed
- **Postcondition:** The registration record holds a permanent reference to the exact version of warranty terms applicable at the customer's date of purchase

---

### UC-010: Present Separate Marketing Consent at Registration

- **Related Requirement:** FR-010
- **Actor:** Customer
- **Precondition:** Customer is completing the warranty registration form on the customer portal
- **Main Flow:**
  1. Customer fills in the mandatory warranty registration fields
  2. Towards the end of the registration form, the system displays a clearly labelled optional section for marketing communications preferences
  3. The marketing opt-in checkbox is displayed as unticked by default, with plain-language text explaining what the customer is agreeing to if they opt in
  4. Customer chooses whether or not to tick the marketing opt-in checkbox
  5. Customer submits the registration form
  6. The system records the warranty registration and the customer's marketing consent choice independently
  7. The warranty registration proceeds regardless of the customer's consent choice
- **Alternative Flow:** Customer does not interact with the marketing consent section at all and submits the form with the checkbox in its default unticked state. The system treats this as no consent given and proceeds with warranty registration only. No marketing communications will be sent to this customer
- **Postcondition:** The warranty registration is complete; a separate marketing consent record is stored reflecting the customer's explicit choice, with no assumption of consent where none was given

---

### UC-011: Record Timestamped Consent Record

- **Related Requirement:** FR-011
- **Actor:** System (automated, triggered at registration or consent change)
- **Precondition:** A customer has interacted with any consent option during registration or has subsequently updated their consent preferences
- **Main Flow:**
  1. Customer makes a consent selection — either opting in or explicitly declining — during registration or through their account settings
  2. The system captures the consent choice, the specific consent category (e.g. warranty processing, marketing email), and the mechanism through which it was given (e.g. registration form, account settings page)
  3. The system records the date and time of the consent action
  4. A consent record is created and stored, linked to the customer's profile
  5. The consent record is immutable — it cannot be modified or deleted, only superseded by a later consent action
- **Alternative Flow:** A customer updates their consent preference — for example, withdrawing marketing consent. Rather than overwriting the existing consent record, the system creates a new consent record with the updated preference and timestamp, preserving the complete consent history
- **Postcondition:** A complete, timestamped, immutable consent history is held for the customer, showing every consent action taken and the mechanism through which it was recorded

---

### UC-012: Withdraw Marketing Consent

- **Related Requirement:** FR-012
- **Actor:** Customer
- **Precondition:** Customer has an active account in the portal and has previously provided marketing consent; customer is either logged into the portal or has clicked an unsubscribe link in a marketing email
- **Main Flow:**
  1. Customer navigates to their account settings in the customer portal, or clicks the unsubscribe link in a marketing email
  2. The system presents the customer's current communication preferences
  3. Customer unchecks or deselects the marketing communications consent option
  4. Customer confirms the change
  5. The system immediately updates the customer's consent status to withdrawn
  6. A new consent record is created with the withdrawal timestamp
  7. The system ceases to include this customer in outbound marketing communications from the moment of withdrawal
- **Alternative Flow:** Customer attempts to withdraw consent via the unsubscribe link but the link has expired or is malformed. The system displays an error message directing the customer to log into their account to manage their preferences, ensuring the withdrawal mechanism remains accessible even if the direct link fails
- **Postcondition:** Marketing consent is recorded as withdrawn with a timestamp; no further marketing communications are sent to this customer until consent is re-established; the previous consent record is preserved for audit purposes

---

### UC-013: View Active Warranties and Claim History via Customer Portal

- **Related Requirement:** FR-013
- **Actor:** Customer
- **Precondition:** Customer has a registered account in the portal and at least one registered warranty
- **Main Flow:**
  1. Customer logs into the customer portal
  2. Customer navigates to the "My Warranties" section
  3. The system retrieves and displays all warranties registered to the customer's account, showing product name, registration date, purchase date, and expiry date for each
  4. Customer selects a specific warranty to view its full details
  5. The system displays the warranty details alongside the customer's claim history for that product, including each claim's reference number, submission date, current status, and resolution outcome
- **Alternative Flow:** Customer has no registered warranties on their account. The system displays a clear message indicating no warranties are registered and provides a direct link to the warranty registration flow
- **Postcondition:** Customer has a complete, current view of all their active warranties and associated claim history without having contacted customer service

---

### UC-014: Submit a Warranty Claim Online

- **Related Requirement:** FR-014
- **Actor:** Customer
- **Precondition:** Customer is logged into the customer portal and has at least one active, non-expired warranty registered to their account
- **Main Flow:**
  1. Customer navigates to the "Submit a Claim" section in the portal
  2. Customer selects the relevant registered warranty from their account
  3. Customer completes the claim form, selecting a fault category, entering a fault description, and uploading supporting documents
  4. Customer reviews the completed claim on a summary screen
  5. Customer confirms and submits the claim
  6. The system validates the submission, saves the claim record, and displays a confirmation screen with the claim reference number
- **Alternative Flow:** Customer attempts to submit a claim but does not have any active warranties on their account. The system displays a message explaining that no eligible warranties were found and directs the customer to register their product first or contact customer service if they believe they have a valid warranty
- **Postcondition:** A warranty claim record is saved in the system with a unique claim reference number, linked to the relevant warranty registration, with all submitted documents stored against the claim record

---

### UC-015: Enforce Mandatory Fields on Claim Submission

- **Related Requirement:** FR-015
- **Actor:** Customer
- **Precondition:** Customer is completing the claim submission form in the customer portal
- **Main Flow:**
  1. Customer fills in all required fields on the claim form: product serial number, fault category (selected from a picklist), fault detail (free text description), and proof of purchase (uploaded document)
  2. Customer clicks Submit
  3. The system validates that all mandatory fields are completed and that a document has been uploaded
  4. All validations pass and the claim is accepted for processing
- **Alternative Flow:** Customer submits the form with the fault category not selected or the proof of purchase document not uploaded. The system highlights the incomplete fields with clear, plain-language error messages and prevents submission. Customer is required to complete all mandatory fields before the form will be accepted. The system does not lose any previously entered data when displaying validation errors
- **Postcondition:** Every claim record created through the customer portal contains a complete set of mandatory data including a fault category code and at least one supporting document

---

### UC-016: Select Fault Category from Structured Picklist

- **Related Requirement:** FR-016
- **Actor:** Customer (during claim submission); Administrator (during picklist maintenance)
- **Precondition:** The claim submission form is open; the fault category picklist has been configured in the system with at least the standard categories: electrical, mechanical, structural, cosmetic
- **Main Flow:**
  1. Customer reaches the fault description section of the claim submission form
  2. The system displays a dropdown or selection list of fault categories
  3. Customer selects the primary fault category that best describes the issue (e.g. Mechanical)
  4. The system displays a secondary picklist of specific fault type codes relevant to the selected primary category
  5. Customer selects the specific fault type code
  6. Customer optionally adds a free-text description to provide additional detail
  7. The structured fault category and code are stored on the claim record
- **Alternative Flow:** Customer cannot find a fault category that accurately describes their issue. Customer selects the closest available category and uses the free-text description field to provide further context. The claim is submitted with the closest available structured code and the additional context. An administrator can review and recode the fault category during claim processing if required
- **Postcondition:** Every submitted claim contains a standardised, structured fault category and fault code that can be used for trend analysis and reporting

---

### UC-017: Generate Claim Reference Number

- **Related Requirement:** FR-017
- **Actor:** System (automated, triggered at successful claim submission)
- **Precondition:** A warranty claim has been successfully validated and saved to the system
- **Main Flow:**
  1. Customer submits a completed claim form
  2. The system validates the submission and saves the claim record
  3. The system automatically generates a unique, non-sequential claim reference number
  4. The reference number is stored against the claim record
  5. The claim reference number is displayed on the confirmation screen immediately after submission
- **Alternative Flow:** Reference number generation fails due to a system error. The system displays an error message to the customer advising that the submission could not be completed and asks them to try again. The partial claim record is not saved. An error event is logged and surfaced to the System Administrator
- **Postcondition:** The claim record has a unique reference number visible to both the customer on the confirmation screen and to internal users in the claims management interface

---

### UC-018: Send Automated Claim Acknowledgement Email

- **Related Requirement:** FR-018
- **Actor:** System (automated, triggered at successful claim submission)
- **Precondition:** A warranty claim has been successfully saved and the customer has a valid email address on their account
- **Main Flow:**
  1. The system confirms that a claim record has been saved
  2. The system generates an acknowledgement email using the configured claim acknowledgement template
  3. The email includes the claim reference number, the product details, a summary of the reported fault, and the expected response timeframe (based on the configured SLA — default 24 hours for acknowledgement, 5 business days for decision)
  4. The system dispatches the email to the customer's registered email address within the configured SLA window
  5. The outbound notification is logged in the notification log with a timestamp and delivery status
- **Alternative Flow:** The acknowledgement email fails to send within the SLA window due to a delivery failure or notification service outage. The failure is logged and surfaced to the System Administrator. The system retries delivery up to the configured retry limit. If retries are exhausted, the administrator is alerted to manually contact the customer
- **Postcondition:** The customer has received an acknowledgement email with a claim reference number and a stated response timeframe; the notification event is recorded in the system log

---

### UC-019: Prevent Claim Submission Against Expired Warranty

- **Related Requirement:** FR-019
- **Actor:** Customer
- **Precondition:** Customer is logged into the customer portal and attempting to submit a claim; the warranty associated with the product has passed its expiry date
- **Main Flow:**
  1. Customer navigates to the claim submission page
  2. Customer selects the product they wish to claim against
  3. The system checks the warranty expiry date for the selected product
  4. The warranty is confirmed as active (not expired) and the customer is permitted to proceed with claim submission
- **Alternative Flow — Warranty expired:** The system determines that the warranty expiry date has passed. The system prevents the claim submission form from being presented and displays a clear, informative message stating that the warranty for this product expired on a specific date and that the claim cannot proceed. The message also advises the customer of any alternative options — for example, contacting customer service if they believe there has been an error — and provides a reference to their statutory rights
- **Postcondition:** No claim record is created against an expired warranty through the self-service portal; the customer receives a clear explanation of why the claim could not be submitted

---

### UC-020: Continue Processing Claim After Warranty Expires Mid-Process

- **Related Requirement:** FR-020
- **Actor:** System (automated rule applied during claim processing)
- **Precondition:** A warranty claim has been submitted while the warranty was active; the warranty expiry date subsequently passes while the claim is still in an open processing state
- **Main Flow:**
  1. Customer submits a claim on day 28 of a 30-day warranty period; the claim is valid and accepted
  2. The claim enters the processing workflow (Submitted → Under Review)
  3. The warranty expiry date passes on day 30 while the claim is still under review
  4. The system checks the claim's submission date against the warranty expiry date
  5. The system confirms that the claim was submitted before the expiry date and applies the warranty terms that were in effect at the date of submission
  6. Processing continues under those terms with no interruption
- **Alternative Flow:** A processor queries whether the claim is still valid given that the warranty has since expired. The system displays the claim submission date prominently alongside the warranty expiry date in the claim record, confirming that the claim was submitted within the valid period. The processor is not required to make a manual judgement on this point
- **Postcondition:** The claim is processed under the terms applicable at the date of submission, regardless of whether the warranty has since expired; the claim record clearly shows the submission date and the applicable terms version

---

### UC-021: Manage Claim Through Defined Lifecycle States

- **Related Requirement:** FR-021
- **Actor:** Claims Processor, System
- **Precondition:** A warranty claim has been submitted and exists in the system; the processor is logged in with a Claims Processor role
- **Main Flow:**
  1. Processor opens the claims queue and selects a newly submitted claim in the Submitted state
  2. Processor reviews the claim details and moves it to Under Review
  3. Processor completes their review and determines the claim has sufficient information to make a decision
  4. Processor selects either Approved or Rejected and records the decision rationale
  5. The system transitions the claim to the selected state and triggers the appropriate customer notification
  6. Processor closes the claim, moving it to the Closed state
- **Alternative Flow:** Processor determines that additional information is required from the customer. Processor moves the claim to Information Requested state. Once the customer responds, the claim moves to Information Received. Processor reviews the additional information and proceeds to a decision. If the matter requires a senior decision, processor escalates and the claim moves to Escalated state
- **Postcondition:** The claim has progressed through the appropriate lifecycle states to a terminal state (Approved, Rejected, or Closed); every state transition is recorded in the claim history

---

### UC-022: Enforce Claim State Transition Rules

- **Related Requirement:** FR-022
- **Actor:** Claims Processor, Supervisor
- **Precondition:** A claim is in an open state; a user attempts to transition it to another state
- **Main Flow:**
  1. Processor selects a state transition action on a claim — for example, moving from Under Review to Approved
  2. The system checks the transition matrix to confirm that the Approved state is a valid destination from Under Review for a user with the Processor role
  3. The transition is permitted and the system executes it
  4. The claim record is updated and the relevant automated actions — such as customer notification — are triggered
- **Alternative Flow:** A processor attempts to transition a claim directly from Submitted to Approved, bypassing the Under Review state. The system checks the transition matrix and determines this is not a permitted transition. The system prevents the action and displays a message explaining that the claim must be moved to Under Review before a decision can be made. The processor is required to follow the correct sequence
- **Postcondition:** All claim state transitions recorded in the system conform to the approved transition matrix; no claim can reach a state through an unauthorised path

---

### UC-023: Log Every Claim State Transition

- **Related Requirement:** FR-023
- **Actor:** System (automated, triggered on every state change)
- **Precondition:** A claim state transition has been triggered by a user action or an automated system event
- **Main Flow:**
  1. A user or automated rule triggers a state transition on a claim
  2. The system records the transition in the claim event log with: the previous state, the new state, the date and time of the transition, and the user ID of the person who triggered it
  3. If the user entered notes with the transition, those notes are stored alongside the log entry
  4. The log entry is written immediately and is immutable — it cannot be edited or deleted
  5. The complete event log is visible to authorised users when viewing the claim record
- **Alternative Flow:** A state transition is triggered by an automated rule (for example, a scheduled SLA breach escalation) rather than a human user. The log entry is created with the System identified as the actor rather than a user ID, and the triggering rule is referenced in the log entry
- **Postcondition:** The claim record contains a complete, timestamped, immutable audit trail of every state transition from submission to closure

---

### UC-024: Send Customer Notification on Claim Status Change

- **Related Requirement:** FR-024
- **Actor:** System (automated, triggered on every claim state change)
- **Precondition:** A claim state transition has occurred; the customer associated with the claim has a valid email address
- **Main Flow:**
  1. A claim state changes — for example, from Under Review to Approved
  2. The system identifies the customer associated with the claim and retrieves their preferred contact channel
  3. The system generates a status update notification using the template configured for the new state, populated with the claim reference number, product details, new status, and any relevant next steps
  4. The notification is dispatched to the customer via their preferred channel
  5. The notification event is logged in the notification log with a timestamp and delivery status
- **Alternative Flow:** The customer's preferred channel is SMS but the customer's phone number is not recorded on their account. The system falls back to email delivery and logs a note that SMS delivery was attempted but fell back to email. The customer is prompted to update their phone number when they next log in
- **Postcondition:** The customer has been informed of every change to their claim status via their preferred channel; all notification events are recorded in the log

---

### UC-025: Record Decision Rationale on Claim

- **Related Requirement:** FR-025
- **Actor:** Claims Processor, Supervisor
- **Precondition:** A claim is in an active state (Under Review, Information Received, or Escalated); the user has selected an Approve, Reject, or Escalate action
- **Main Flow:**
  1. Processor selects the decision action on a claim — for example, Reject
  2. The system presents a mandatory rationale field before the transition can be confirmed
  3. Processor selects the applicable rejection reason code from the controlled vocabulary picklist
  4. Processor enters any additional free-text notes to support the rationale
  5. Processor confirms the decision
  6. The system saves the rationale alongside the state transition in the claim event log and completes the transition
- **Alternative Flow:** Processor attempts to confirm a decision without completing the rationale field. The system prevents the transition from completing and displays a message requiring the rationale to be entered before proceeding. The claim status does not change until the rationale is recorded
- **Postcondition:** Every decision transition (approval, rejection, escalation) on a claim has a documented rationale stored permanently in the claim record

---

### UC-026: Send Information Request to Customer from Claim Record

- **Related Requirement:** FR-026
- **Actor:** Claims Processor
- **Precondition:** A claim is in the Under Review state; the processor has determined that additional information is required from the customer before a decision can be made
- **Main Flow:**
  1. Processor selects the "Request Information" action from within the claim record
  2. The system presents a message composition interface pre-populated with the claim reference and customer details
  3. Processor types the specific information request — for example, asking for a clearer photo of the fault or a copy of the original receipt
  4. Processor confirms and sends the request
  5. The system transitions the claim to the Information Requested state, logs the request with a timestamp, and sends an email notification to the customer containing the processor's message and instructions for responding through the portal
- **Alternative Flow:** The processor sends an information request but the customer's email is undeliverable. The delivery failure is logged and surfaced to the processor. The processor contacts the customer service team to attempt to reach the customer through an alternative channel. The claim remains in the Information Requested state with a log note recording the delivery failure
- **Postcondition:** The claim is in the Information Requested state; the customer has been notified of what is required; the information request is recorded in the claim event log

---

### UC-027: Customer Responds to Information Request via Portal

- **Related Requirement:** FR-027
- **Actor:** Customer
- **Precondition:** Customer has received an information request notification; the claim is in the Information Requested state; customer is logged into the portal
- **Main Flow:**
  1. Customer receives an email notification informing them that additional information is required for their claim
  2. Customer clicks the link in the email, which takes them to the relevant claim in their portal account
  3. The claim displays the processor's information request message prominently
  4. Customer uploads the requested document or enters the requested information using the response interface
  5. Customer submits the response
  6. The system transitions the claim from Information Requested to Information Received, logs the customer's response with a timestamp, and sends a notification to the assigned processor that a response has been received
- **Alternative Flow:** Customer logs into the portal and finds the information request but does not have the requested document available. Customer sends a message through the portal explaining the delay and providing an estimated timeframe. The claim remains in Information Requested state and the customer's message is logged. The processor receives a notification of the customer's response
- **Postcondition:** The customer's response is stored against the claim record; the claim has transitioned to Information Received; the assigned processor has been notified

---

### UC-028: Escalate a Claim to Supervisor

- **Related Requirement:** FR-028
- **Actor:** Claims Processor
- **Precondition:** A claim is in an active processing state; the processor has encountered a scenario — such as ambiguous warranty terms, a disputed purchase date, or potential fraud — that requires a supervisor decision
- **Main Flow:**
  1. Processor selects the "Escalate" action on the claim record
  2. The system presents a mandatory escalation reason field
  3. Processor selects the escalation reason from a controlled list and adds supporting notes
  4. Processor confirms the escalation
  5. The system transitions the claim to the Escalated state, logs the escalation with the processor's identity, timestamp, and reason
  6. The system sends a notification to the assigned Supervisor alerting them that a new escalated claim requires their attention
- **Alternative Flow:** The processor attempts to escalate a claim that is already in the Escalated state — for example, after a supervisor has already reviewed it and returned it to the processor without closing the escalation. The system prevents a duplicate escalation and displays the current escalation history, prompting the processor to resolve the existing escalation thread first
- **Postcondition:** The claim is in the Escalated state; the supervisor has been notified; the escalation reason is documented in the claim event log; the claim is visible in the supervisor's escalation queue

---

### UC-029: View All Escalated Claims as Supervisor

- **Related Requirement:** FR-029
- **Actor:** Warranty Operations Manager / Supervisor
- **Precondition:** The supervisor is logged in with a Supervisor or Operations Manager role; one or more claims are in the Escalated state
- **Main Flow:**
  1. Supervisor navigates to the escalation management view in the internal dashboard
  2. The system displays all claims currently in the Escalated state, showing for each: claim reference number, product, customer name, escalating processor, date of escalation, time elapsed in escalated state, and escalation reason
  3. Supervisor selects an escalated claim to review in full
  4. Supervisor reviews the claim details, the escalation reason, and the full claim history
  5. Supervisor makes a decision — approves, rejects, or returns to the processor with guidance — and records their decision rationale
  6. The claim transitions to the appropriate next state
- **Alternative Flow:** Supervisor opens the escalation view and finds no claims currently escalated. The system displays a clear empty state message confirming that no escalated claims require attention
- **Postcondition:** The supervisor has a complete, up-to-date view of all escalated claims; each escalation review and decision is logged against the claim record

---

### UC-030: Claims Processor Submits Claim on Behalf of Customer

- **Related Requirement:** FR-030
- **Actor:** Customer Service Representative
- **Precondition:** A customer has contacted the organisation by phone or in person and wishes to submit a claim but cannot or will not use the self-service portal; the CSR is logged in with a Customer Service Representative role
- **Main Flow:**
  1. CSR navigates to the customer's profile in the internal portal and locates the relevant registered warranty
  2. CSR selects "Submit Claim on Behalf of Customer" from the warranty record
  3. The system presents the same claim submission form as the customer-facing version, with the customer's details and warranty pre-populated
  4. CSR enters the fault category (from the picklist), the fault description as described by the customer, and uploads any proof of purchase document provided by the customer
  5. CSR reviews the completed form with the customer and confirms submission
  6. The system creates the claim record, generates a claim reference number, and sends the standard acknowledgement email to the customer
  7. The claim record is marked to indicate it was submitted by a CSR on behalf of a customer
- **Alternative Flow:** The customer does not have a registered warranty. The CSR searches for the customer's product by serial number and purchase date and, if a valid warranty is confirmed, creates both the registration and the claim in the same session. Both records are flagged as created by CSR on behalf of customer
- **Postcondition:** A fully documented claim record exists in the system; the customer receives the same acknowledgement email they would receive through self-service; the claim origin is recorded for audit purposes

---

### UC-031: View Unified Customer Profile Screen

- **Related Requirement:** FR-031
- **Actor:** Customer Service Representative
- **Precondition:** CSR is logged in with a Customer Service Representative role; the customer has an account in the system
- **Main Flow:**
  1. Customer contacts the organisation and provides their name, email address, or warranty reference number
  2. CSR searches for the customer in the internal portal
  3. The system displays the customer's full profile on a single screen showing: all active warranties with expiry dates, all open claims with current status, all previous claims history, and a log of recent contact events
  4. CSR can immediately see the customer's full warranty situation without navigating to any other screen or system
  5. CSR uses the information to respond to the customer's query
- **Alternative Flow:** The customer cannot be found by name, email, or reference number. The CSR is prompted to search by serial number or to create a new customer record if the customer is not yet registered in the system
- **Postcondition:** The CSR has all relevant warranty and contact information for the customer visible in a single screen within seconds of beginning the interaction

---

### UC-032: Display All Warranty and Claim Data on Unified Customer Profile

- **Related Requirement:** FR-032
- **Actor:** Customer Service Representative, Customer (via self-service portal)
- **Precondition:** The customer has at least one warranty or claim record in the system; the user is viewing the customer's profile
- **Main Flow:**
  1. User opens the customer profile screen
  2. The system queries all modules — warranty registrations, claims, notifications, contact history — for records linked to that customer
  3. All relevant data is assembled and displayed on the single profile screen: warranties in one section, open claims in another, closed claims history below, recent communications at the bottom
  4. Each section shows the most critical information at a glance with the option to expand for full detail
  5. No navigation to a separate module or system is required to access any of this information
- **Alternative Flow:** The customer profile exists but has no linked warranties or claims. The system displays the profile with clearly labelled empty sections for each category, with call-to-action links to register a warranty or submit a claim
- **Postcondition:** All warranty and claims data for the customer is visible from a single screen in the system

---

### UC-033: Processor Views Registration Record from Within Claim Screen

- **Related Requirement:** FR-033
- **Actor:** Claims Processor
- **Precondition:** A claims processor is reviewing a claim; the claim is linked to a warranty registration record
- **Main Flow:**
  1. Processor opens a claim record in the claims processing interface
  2. Within the claim screen, the system displays a summary panel showing the linked warranty registration details: registration date, purchase date, retailer, warranty period, expiry date, and previous claims on the same serial number
  3. Processor can see all relevant registration context without leaving the claim screen
  4. If the processor needs to view the full registration record, they can expand the panel or navigate to it in a new tab with a single click
  5. Processor uses the registration context to make a fully informed claim decision
- **Alternative Flow:** The claim record has been submitted with a serial number that has no linked registration in the system. The claim screen displays a flag indicating that no registration record was found for this serial number, and prompts the processor to investigate before proceeding. The processor may escalate or request information from the customer
- **Postcondition:** The claims processor has access to all relevant registration information at the point of claim review without switching systems or making manual enquiries

---

### UC-034: Send Warranty Expiry Reminder Notifications

- **Related Requirement:** FR-034
- **Actor:** System (automated, triggered by date-based rules)
- **Precondition:** Registered warranties with a recorded expiry date exist in the system; the customer has a valid contact channel recorded; the configured notification trigger dates (60 days and 30 days before expiry) have been reached
- **Main Flow:**
  1. The system runs a daily scheduled job checking all active warranty records for approaching expiry dates
  2. The system identifies all warranties where the expiry date is exactly 60 days from today
  3. For each identified warranty, the system generates an expiry reminder notification using the configured 60-day reminder template
  4. The notification is dispatched to the customer via their preferred contact channel
  5. The same process runs again and identifies all warranties expiring in exactly 30 days, dispatching the 30-day reminder
  6. All notification events are logged in the notification log
- **Alternative Flow:** A customer has opted out of all non-essential communications. The system checks the customer's notification preferences before dispatching the reminder. If the expiry reminder is classified as a service communication (not marketing), the system sends it regardless of marketing opt-out status. If the customer has opted out of all communications, the system logs the suppression and does not send the notification
- **Postcondition:** Customers with warranties approaching expiry have been notified at both the 60-day and 30-day milestones via their preferred channel; all notification events are recorded

---

### UC-035: Manage Notification Templates via Admin Interface

- **Related Requirement:** FR-035
- **Actor:** System Administrator
- **Precondition:** The System Administrator is logged in; a notification template exists in the system (e.g. registration confirmation, expiry reminder, claim status update)
- **Main Flow:**
  1. System Administrator navigates to the Notification Templates section of the admin interface
  2. The system displays a list of all configured notification templates with their type, status, and last modified date
  3. Administrator selects a template to edit — for example, the 60-day expiry reminder
  4. The system displays the template in an editable format, showing the subject line, body content, and any dynamic field placeholders (e.g. customer name, expiry date)
  5. Administrator makes the required changes — for example, updating the body text to include a seasonal promotion message in line with current marketing guidance
  6. Administrator saves the updated template
  7. The system records the change in the admin audit log, noting who made the change and when
- **Alternative Flow:** Administrator saves a template with an invalid or missing dynamic field placeholder that would cause the email to render incorrectly. The system validates the template before saving and highlights the problematic placeholder with a clear error message. The template is not saved until the error is corrected
- **Postcondition:** The updated notification template is active and will be used for all future notifications of that type; the change is recorded in the audit log; no developer involvement was required

---

### UC-036: Log All Outbound Notifications

- **Related Requirement:** FR-036
- **Actor:** System (automated, triggered on every outbound notification dispatch)
- **Precondition:** The system has dispatched or attempted to dispatch a notification to a customer
- **Main Flow:**
  1. The system dispatches a notification — for example, a claim acknowledgement email
  2. Immediately upon dispatch, the system creates a notification log entry recording: notification type, recipient identifier, dispatch timestamp, channel used (email/SMS), and initial delivery status
  3. The email service returns a delivery confirmation
  4. The system updates the notification log entry with the confirmed delivery status and timestamp
  5. The log entry is available to authorised internal users when viewing the relevant claim or warranty record
- **Alternative Flow:** The notification service returns a delivery failure for a specific recipient — for example, a hard bounce due to an invalid email address. The system updates the notification log entry with a failed status and the failure reason. The failure is surfaced to the System Administrator in the daily health report and, if above a threshold number of failures, an immediate alert is triggered
- **Postcondition:** Every outbound notification attempt has a complete log entry including delivery outcome; delivery failures are visible to the System Administrator and associated with the relevant customer or claim record

---

### UC-037: Retry Failed Notifications with Exponential Backoff

- **Related Requirement:** FR-037
- **Actor:** System (automated, triggered on notification delivery failure)
- **Precondition:** A notification dispatch has been attempted and a delivery failure has been recorded in the notification log
- **Main Flow:**
  1. The system detects a notification delivery failure on initial dispatch
  2. The system schedules a first retry after the configured initial backoff interval (e.g. 5 minutes)
  3. The retry is attempted; if still unsuccessful, the system schedules a second retry with a doubled backoff interval (e.g. 10 minutes)
  4. Each subsequent retry doubles the backoff interval up to the configured maximum retry count
  5. On any successful retry, the notification log is updated with the successful delivery status and the number of retry attempts made
  6. The customer receives the notification and the process ends
- **Alternative Flow:** All configured retry attempts are exhausted without successful delivery. The system updates the notification log entry with a final failed status and the total number of attempts. The failure is escalated to the System Administrator via an alert. The administrator reviews the failure and determines whether manual follow-up with the customer is required
- **Postcondition:** Failed notifications are retried automatically up to the configured limit; all retry attempts and outcomes are logged; persistent failures are escalated to the administrator for manual resolution

---

### UC-038: View Personal Claims Queue in Internal Dashboard

- **Related Requirement:** FR-038
- **Actor:** Claims Processor
- **Precondition:** Claims Processor is logged into the internal claims management application with a Processor role
- **Main Flow:**
  1. Processor logs in and lands on their personal claims dashboard
  2. The system displays all claims currently assigned to this processor, showing for each: claim reference, customer name, product, submission date, claim age in business days, and current status
  3. Claims approaching the SLA threshold are highlighted prominently
  4. Processor selects a claim from the queue to begin processing
  5. The dashboard updates in real time as the processor works through claims
- **Alternative Flow:** The processor's queue is empty — all assigned claims have been processed or reassigned. The system displays a clear empty state message. If there are unassigned claims available in the shared pool, the system highlights this with a prompt to pick up additional work
- **Postcondition:** The processor has a clear, real-time view of their personal workload, claim ages, and SLA status at all times during their working day

---

### UC-039: View Real-Time Operations Dashboard as Manager

- **Related Requirement:** FR-039
- **Actor:** Warranty Operations Manager
- **Precondition:** Operations Manager is logged in with a Supervisor or Manager role
- **Main Flow:**
  1. Operations Manager logs in and navigates to the operational overview dashboard
  2. The system displays three primary data panels: (1) total claims in queue broken down by processor and by age in business days, (2) claims currently awaiting customer response separated from the actionable queue, and (3) currently open escalations with time elapsed since escalation
  3. The dashboard refreshes automatically at a configured interval (e.g. every 5 minutes) to maintain near-real-time accuracy
  4. Manager reviews the dashboard at the start of the day to assess workload distribution and identify any items requiring immediate attention
  5. Manager drills into any panel to see the individual claim records behind the summary figures
- **Alternative Flow:** The dashboard data cannot be refreshed due to a system connectivity issue. The system displays the last-loaded data with a visible timestamp indicating when it was last updated and a warning that the data may not be current. The manager is not presented with a blank screen
- **Postcondition:** The Operations Manager has a live, consolidated view of team workload, queue age, pending customer responses, and open escalations without needing to request a manual report from individual processors

---

### UC-040: Flag Claims Approaching SLA Breach

- **Related Requirement:** FR-040
- **Actor:** System (automated, triggered by SLA threshold rules)
- **Precondition:** Claims exist in the system in active processing states; the SLA threshold is configured (default: flag claims older than 3 business days)
- **Main Flow:**
  1. The system runs a continuous background check against the age of all open claims
  2. A claim is identified that has been in an active state for 3 business days without reaching a decision state
  3. The system flags the claim with a visual SLA warning indicator in the processor's queue view
  4. The same flag appears in the Operations Manager's real-time dashboard under the relevant processor's queue
  5. The system logs the SLA flag event against the claim record
- **Alternative Flow:** A claim has been in the Information Requested state — waiting for customer response — for longer than the SLA threshold. The system applies a different flag to distinguish between claims that are delayed due to internal processing and those that are delayed awaiting external customer response, so that the manager does not incorrectly attribute the delay to the processor
- **Postcondition:** Claims at risk of SLA breach are prominently flagged to both the processor and the manager; the distinction between internal and customer-caused delays is visible in the dashboard

---

### UC-041: Reassign Claims Between Processors

- **Related Requirement:** FR-041
- **Actor:** Warranty Operations Manager
- **Precondition:** Operations Manager is logged in with a Manager role; two or more processors have active queues; a reason to redistribute work exists (e.g. peak volume, processor absence)
- **Main Flow:**
  1. Manager navigates to the queue management section of the operations dashboard
  2. Manager reviews the claim distribution across processors
  3. Manager selects one or more claims from an overloaded processor's queue
  4. Manager assigns the selected claims to a different processor
  5. The system updates the claim assignment records and adds a log entry noting the reassignment, the manager who performed it, and the timestamp
  6. The receiving processor's queue updates immediately to include the newly assigned claims
  7. Both processors receive a notification of the reassignment
- **Alternative Flow:** Manager attempts to reassign a claim that a processor has already opened for active review. The system warns the manager that the claim is currently open by the assigned processor and asks for confirmation before proceeding with the reassignment. The manager confirms and the reassignment proceeds
- **Postcondition:** Selected claims are reassigned to the new processor; both processors' queues are updated in real time; the reassignment is recorded in the claim event log

---

### UC-042: Issue a Processing Hold on Claims

- **Related Requirement:** FR-042
- **Actor:** System Administrator or Warranty Operations Manager
- **Precondition:** An event has occurred — such as a product recall, a policy change, or an identified processing error — that requires all claims for specific products or product categories to be paused pending review
- **Main Flow:**
  1. Manager navigates to the Processing Hold management section of the admin interface
  2. Manager creates a new processing hold, selecting the affected product or product category and entering a mandatory reason
  3. Manager confirms the hold
  4. The system immediately flags all open claims for the affected products with a hold indicator in all processors' queues
  5. The system sends a broadcast notification to all active processors informing them of the hold, the affected products, and the reason
  6. Processors are required to acknowledge the notification before the hold flag will be cleared from their view
  7. The hold and all acknowledgements are logged in the admin audit trail
- **Alternative Flow:** A processor is offline when the broadcast notification is sent. When that processor next logs in, the system presents the unacknowledged hold notification immediately and requires acknowledgement before the processor can access their queue
- **Postcondition:** All affected claims are visibly held in processor queues; all processors have been notified and have acknowledged the hold; the hold and its reason are recorded in the system audit log

---

### UC-043: Assign and Enforce Role-Based Access Control

- **Related Requirement:** FR-043
- **Actor:** System Administrator
- **Precondition:** A new internal user account is being created or an existing user's role is being modified; the System Administrator is logged in
- **Main Flow:**
  1. System Administrator navigates to the user management section of the admin interface
  2. Administrator creates a new user account and assigns the appropriate role from the defined role list: Customer Service Representative, Claims Processor, Supervisor, Data Analyst (read-only), System Administrator, or IT Security Auditor
  3. The system applies the permissions associated with that role immediately upon assignment
  4. The user logs in and can only access the features and data permitted for their assigned role
  5. Any attempt by the user to access a resource outside their role permissions is silently blocked or returns a not-permitted message
- **Alternative Flow:** A user attempts to access a function above their permission level — for example, a Claims Processor attempts to access the admin configuration panel. The system denies access and displays a message indicating that the action requires a higher permission level. The attempted access is logged in the security audit trail
- **Postcondition:** Every internal user account has a defined role; system access is limited to the permissions of the assigned role; all access attempts, including denied ones, are logged

---

### UC-044: Authenticate Internal Users via Azure Active Directory

- **Related Requirement:** FR-044
- **Actor:** Internal User (any staff role)
- **Precondition:** The system is integrated with the organisation's Azure Active Directory; the user has an active Azure AD account
- **Main Flow:**
  1. Internal user navigates to the warranty management system's internal login page
  2. The system redirects the user to the organisation's Azure AD single sign-on page
  3. User authenticates using their standard organisational credentials (and MFA if required by AD policy)
  4. Azure AD confirms successful authentication and returns the user's identity and role group membership to the warranty system
  5. The warranty system maps the user's AD group to the appropriate internal role and grants access
  6. User is logged in and presented with the interface appropriate to their role
- **Alternative Flow:** The user's Azure AD account has been disabled — for example, they have left the organisation. Authentication fails and the user is denied access. The warranty system receives the failure response from AD and does not grant access. No separate deactivation step in the warranty system is required
- **Postcondition:** The user is authenticated via the single organisational identity provider; no separate warranty system password is required; access is immediately revoked when the AD account is disabled

---

### UC-045: Register and Log In as Customer via Standalone Authentication

- **Related Requirement:** FR-045
- **Actor:** Customer
- **Precondition:** Customer does not have an existing account in the customer portal
- **Main Flow:**
  1. Customer navigates to the customer portal and selects "Create an Account"
  2. Customer enters their email address and creates a password that meets the configured complexity requirements
  3. Customer submits the registration form
  4. The system creates the account and immediately sends an email verification message to the provided address
  5. Customer clicks the verification link in the email
  6. The system marks the email address as verified and activates the account
  7. Customer can now log in to the portal using their email and password
- **Alternative Flow:** Customer enters an email address that is already associated with an existing account. The system does not confirm whether the email is registered (to prevent account enumeration) and instead displays a standard message advising the customer to check their email for a verification link or to use the password reset function if they have forgotten their credentials
- **Postcondition:** The customer has a verified, active account in the customer portal accessible via email and password credentials

---

### UC-046: Reset Customer Portal Password

- **Related Requirement:** FR-046
- **Actor:** Customer
- **Precondition:** Customer has an existing verified account in the customer portal and has forgotten their password or has been locked out
- **Main Flow:**
  1. Customer navigates to the customer portal login page and selects "Forgot Password"
  2. Customer enters their registered email address
  3. The system sends a password reset link to that email address if an account exists for it; if no account exists, the system sends the same response message to prevent email enumeration
  4. Customer clicks the password reset link in the email within the link's validity window (configurable, default 60 minutes)
  5. Customer is taken to a secure reset form and enters a new password meeting complexity requirements
  6. Customer confirms the new password and submits
  7. The system updates the password, invalidates any existing active sessions for that account, and confirms the reset to the customer
- **Alternative Flow:** Customer does not receive the reset email — for example, it was filtered to spam or was not delivered. Customer returns to the portal and can request a new reset link. If the link has already expired, the system generates a fresh link on the new request. If the customer remains unable to access their account, they are directed to contact customer service for assisted account recovery
- **Postcondition:** Customer's password has been successfully reset; previous sessions are invalidated; the password reset event is logged in the account audit trail

---

### UC-047: Maintain Product Catalogue via Admin Interface

- **Related Requirement:** FR-047
- **Actor:** System Administrator
- **Precondition:** The System Administrator is logged in; a new product line is being launched or an existing product's warranty details need updating
- **Main Flow:**
  1. Administrator navigates to the Product Catalogue section of the admin interface
  2. Administrator selects "Add New Product" or selects an existing product to edit
  3. The system presents a product record form with fields for: product model code, product name, product category, warranty duration (in months), and effective date of warranty terms
  4. Administrator completes or updates the required fields
  5. Administrator saves the product record
  6. The updated product is immediately available for warranty registration and claim validation in the system
  7. The change is logged in the admin audit trail with the administrator's identity and timestamp
- **Alternative Flow:** Administrator attempts to save a product record with a duplicate model code that already exists in the catalogue. The system detects the duplicate and displays a clear error message, preventing the record from being saved until the model code conflict is resolved
- **Postcondition:** The product catalogue contains the new or updated product record; the change is live immediately for use in registrations and claims; the change is recorded in the audit log

---

### UC-048: Add, Edit, or Deactivate Product Records

- **Related Requirement:** FR-048
- **Actor:** System Administrator
- **Precondition:** System Administrator is logged in with admin access; a product record exists in the catalogue or is being created
- **Main Flow:**
  1. Administrator navigates to the Product Catalogue management screen
  2. For a new product: Administrator selects "Add Product," completes all required fields, and saves
  3. For an existing product: Administrator locates the product using the search function, selects Edit, modifies the relevant fields (e.g. updates warranty duration from 12 months to 24 months), and saves
  4. For deactivation: Administrator locates the product, selects Deactivate, enters a reason for deactivation, and confirms
  5. A deactivated product is no longer available for new warranty registrations but all existing registrations linked to that product remain accessible
  6. All changes are written to the admin audit log
- **Alternative Flow:** Administrator attempts to deactivate a product that has open active claims linked to it. The system warns the administrator that active claims exist for this product and asks for confirmation before proceeding. The deactivation proceeds but the active claims are unaffected
- **Postcondition:** The product catalogue reflects the current product and warranty configuration; deactivated products are preserved for historical record integrity but unavailable for new registrations

---

### UC-049: Manage User Accounts via Admin Interface

- **Related Requirement:** FR-049
- **Actor:** System Administrator
- **Precondition:** System Administrator is logged in; a new staff member has joined, or an existing user's role has changed, or a staff member has left the organisation
- **Main Flow:**
  1. Administrator navigates to the User Management section of the admin interface
  2. For a new user: Administrator selects "Create User," enters the user's details and email address, assigns their role, and saves — the system provisions the account and sends an activation notification
  3. For a role change: Administrator locates the user account, selects Edit, changes the assigned role, and saves — the new permissions take effect immediately at the user's next login or immediately if they are currently active
  4. For account deactivation: Administrator locates the user account, selects Deactivate, and confirms — the user is logged out immediately if active and cannot log back in
  5. All user management actions are logged in the admin audit trail
- **Alternative Flow:** Administrator attempts to deactivate a user account that currently has one or more open claims assigned to it. The system warns the administrator and automatically reassigns those claims to an unassigned queue for the Operations Manager to redistribute before confirming the deactivation
- **Postcondition:** The user account reflects the required status (active, role-modified, or deactivated); any impacts on open work items are handled before the change takes effect; all changes are recorded in the audit log

---

### UC-050: Automatically Deactivate User Account on Azure AD Disable

- **Related Requirement:** FR-050
- **Actor:** System (automated, triggered by Azure AD account status change)
- **Precondition:** A staff member's Azure AD account has been disabled — typically when they leave the organisation; the warranty system is integrated with Azure AD
- **Main Flow:**
  1. HR or IT disables the departing staff member's Azure AD account
  2. The warranty system's Azure AD integration detects the account status change at the next synchronisation interval
  3. The system immediately deactivates the corresponding internal user account
  4. If the user has an active session in the warranty system at the time, that session is terminated and the user is logged out
  5. The account deactivation is logged in the admin audit trail with the timestamp of the AD-triggered event
- **Alternative Flow:** The Azure AD synchronisation is temporarily delayed due to a connectivity issue. The user's warrant system account remains active during the gap. The system raises an alert if the synchronisation has not run within the configured maximum interval, prompting the administrator to manually deactivate the account if required
- **Postcondition:** The user can no longer access the warranty system once their Azure AD account is disabled; access is revoked without requiring a separate manual step in the warranty system

---

### UC-051: Generate Operational Claims Reports

- **Related Requirement:** FR-051
- **Actor:** Warranty Operations Manager, Data Analyst, Business Sponsor
- **Precondition:** The user is logged in with a role that has access to the reporting module; claims data exists in the system
- **Main Flow:**
  1. User navigates to the reporting module
  2. User selects the report type — for example, "Claims Performance Summary"
  3. User applies filters: product category, specific product model, time period, processor, and resolution outcome
  4. User runs the report
  5. The system retrieves the data and presents the report showing: claim volumes, approval rates, rejection rates, rejection reasons, average processing time, and SLA compliance percentage
  6. User can export the report to CSV or PDF
- **Alternative Flow:** The user applies filter combinations that return no data — for example, filtering by a product that had no claims in the selected period. The system returns the report with zero-value results and a clear message indicating that no claims matched the selected criteria, rather than displaying an error
- **Postcondition:** The user has generated a formatted report based on live system data, filtered to their required scope, without making a manual data request to the analytics team

---

### UC-052: Access Reporting Module Without Data Request

- **Related Requirement:** FR-052
- **Actor:** Product Manager, Warranty Operations Manager, Data Analyst
- **Precondition:** The user is logged in with a role that includes read access to the reporting module; the reporting module is available within the system
- **Main Flow:**
  1. User logs into the system
  2. User navigates to the reporting module without needing to request data from anyone
  3. User selects a report type and applies desired filters
  4. The system generates and displays the report immediately from live data
  5. User exports or saves the report as needed
- **Alternative Flow:** The user's role does not include access to the reporting module — for example, a Claims Processor role. The user navigates to the reporting section and the system displays a message indicating that reporting access is not available for their current role, with a contact for the System Administrator to request access if required
- **Postcondition:** Authorised users can independently access and generate warranty and claims reports without routing through the data analytics team; access is enforced by role

---

### UC-053: Trigger Alert When SLA Compliance Falls Below Threshold

- **Related Requirement:** FR-053
- **Actor:** System (automated, triggered by SLA compliance monitoring)
- **Precondition:** SLA compliance is being monitored by the system; the compliance threshold is configured (default: 80%); a weekly SLA compliance calculation has fallen below the threshold
- **Main Flow:**
  1. The system calculates SLA compliance for the current week — the percentage of claims reaching a decision within the defined turnaround period
  2. The calculated compliance figure falls below the configured 80% threshold
  3. The system generates an alert notification
  4. The alert is sent to the Operations Manager and the Business Sponsor via their configured notification channel
  5. The alert message includes the current compliance figure, the threshold, the number of claims that breached SLA, and a link to the relevant report in the system
- **Alternative Flow:** The SLA compliance figure is exactly at the threshold (80%). The system does not trigger an alert, as the trigger condition is below the threshold, not at it. The Operations Manager can still see the figure on their dashboard and take proactive action if desired
- **Postcondition:** The Operations Manager and Business Sponsor have been automatically notified of an SLA compliance issue before it is escalated to them through a customer complaint; the alert is logged in the system

---

### UC-054: Alert Product Manager When Claim Rate Exceeds Threshold

- **Related Requirement:** FR-054
- **Actor:** System (automated, triggered by claim rate monitoring)
- **Precondition:** Claim rate monitoring is configured for the product; a threshold has been set (e.g. 2% of units sold within the first 6 months post-launch); sales volume data is available in the system for the relevant product
- **Main Flow:**
  1. The system periodically calculates claim rates for each product model — claims received as a percentage of units sold within the configured time window
  2. The calculated claim rate for a specific product exceeds the configured threshold
  3. The system generates an alert and sends it to the assigned Product Manager for that product
  4. The alert includes the product model, the current claim rate, the threshold, the number of claims received, the baseline units sold, and a link to the detailed claims report for that product
  5. The alert is logged in the system against the product record
- **Alternative Flow:** Sales volume data is not available in the system for the affected product, making it impossible to calculate a claim rate as a percentage of units sold. The system sends a notification to the administrator indicating that the claim rate alert cannot be calculated for that product due to missing sales data, and prompts the administrator to update the sales volume figures
- **Postcondition:** The Product Manager has been automatically alerted to a potential product quality issue at the earliest detectable point; the alert is logged for audit purposes

---

### UC-055: Export Data to External Data Warehouse

- **Related Requirement:** FR-055
- **Actor:** System (automated, scheduled export job)
- **Precondition:** An SFTP destination has been configured in the system; the export schedule is active; warranty and claims data exists in the system
- **Main Flow:**
  1. The scheduled export job runs at the configured time (e.g. nightly at 2am)
  2. The system queries all warranty registration and claims records created or updated since the last export run
  3. The system generates a structured export file in the configured format (CSV or JSON)
  4. The system connects to the configured SFTP destination and transfers the file
  5. The transfer is confirmed successful by the SFTP server
  6. The system logs the export job execution with a timestamp, record count, file size, and success status
- **Alternative Flow:** The SFTP transfer fails — for example, due to a network issue or incorrect credentials. The system logs the failure, retries up to the configured retry limit, and if retries are exhausted, sends an alert to the System Administrator. The failed export is flagged for manual review; the next scheduled run will include all records that were not successfully exported in the failed run
- **Postcondition:** An up-to-date, structured export file containing warranty and claims data has been delivered to the configured SFTP destination for ingestion into the organisation's data warehouse

---

### UC-056: Store Proof of Purchase as Immutable Record

- **Related Requirement:** FR-056
- **Actor:** Customer (upload), System (storage enforcement)
- **Precondition:** A customer is submitting a warranty claim and uploads a proof of purchase document; the claim has been saved to the system
- **Main Flow:**
  1. Customer uploads a proof of purchase document as part of a claim submission
  2. The system validates the file type and scans for malware before accepting the upload
  3. The file passes validation and is stored in a write-protected storage location linked to the claim record
  4. The stored document is assigned a reference identifier and its original file hash is recorded to enable integrity verification
  5. The document is accessible to authorised internal users for the duration of the retention period but cannot be modified, overwritten, or deleted by any user role
- **Alternative Flow:** A Claims Processor or Administrator attempts to delete or replace an uploaded proof of purchase document. The system prevents the action and displays a message indicating that uploaded proof of purchase documents are immutable records and cannot be modified. If the processor believes the document was uploaded in error, they must escalate to the administrator who can add a supplementary document note but cannot remove the original
- **Postcondition:** The proof of purchase document is stored permanently and immutably against the claim record, exactly as submitted by the customer, and is available for legal or audit purposes throughout the retention period

---

### UC-057: Export Complete Claim History for Legal or Tribunal Use

- **Related Requirement:** FR-057
- **Actor:** Legal / Compliance Officer, Warranty Operations Manager
- **Precondition:** A claim record exists in the system; a legal dispute, consumer tribunal case, or regulatory enquiry has been received; the user is logged in with appropriate access
- **Main Flow:**
  1. User navigates to the claim record using the claim reference number
  2. User selects "Export Full Claim History"
  3. The system compiles a complete, chronologically ordered report containing: all original submission data and documents, every state transition with timestamps and user identities, all communications sent to the customer, all processor notes and decision rationale entries, the version of warranty terms applicable at time of submission, and all uploaded documents
  4. The system generates the export as a PDF or structured document
  5. User downloads the export for use as evidence
- **Alternative Flow:** The claim record involves a state or document that has been partially corrupted or is missing from the record — for example, an older claim from before the system was fully operational. The system generates the history with available data, clearly flagging any gaps in the record with explanatory notes. The Legal Officer is advised of the data limitations
- **Postcondition:** A complete, ordered, timestamped claim history has been exported in a format suitable for submission to a consumer tribunal or regulatory body

---

### UC-058: Generate Point-in-Time Reports

- **Related Requirement:** FR-058
- **Actor:** Data Analyst, Legal / Compliance Officer
- **Precondition:** Historical warranty and claims data exists in the system; the user is logged in with reporting access
- **Main Flow:**
  1. User navigates to the reporting module and selects "Point-in-Time Report"
  2. User enters the desired historical date — for example, 31 December of the previous year
  3. User selects the report scope — for example, all open claims as of that date
  4. The system queries the historical data, reconstructing the state of records as they existed on the specified date using the claim event logs and audit trail
  5. The system generates and displays the report reflecting the system state as of the specified date
  6. User exports the report for use in annual reporting or legal proceedings
- **Alternative Flow:** The user specifies a date that predates the system's go-live date. The system is unable to generate a report for that period as historical data does not exist. The system displays a message explaining the limitation and indicates the earliest date for which a point-in-time report can be produced
- **Postcondition:** A report reflecting the verified historical state of warranty and claims data as of the specified date has been generated and is available for export

---

### UC-059: Apply Automated Data Retention Policy

- **Related Requirement:** FR-059
- **Actor:** System (automated, scheduled retention management job)
- **Precondition:** Customer personal data records exist in the system; retention periods have been configured (default: warranty period plus 6 years from claim closure)
- **Main Flow:**
  1. The system runs a scheduled retention review job at the configured interval (e.g. monthly)
  2. The system identifies customer records where the configured retention period has been reached or exceeded
  3. For each identified record, the system either anonymises the personal data — replacing PII fields with anonymised values while retaining statistical records — or deletes the record in accordance with the configured policy
  4. Each retention action is logged in the audit trail with the record identifier, action taken, and timestamp
  5. A summary of retention actions taken is made available to the Data Protection Officer or System Administrator in the admin interface
- **Alternative Flow:** A record identified for deletion or anonymisation has an active open claim or an unresolved data subject request linked to it. The system flags the record as requiring manual review before the automated retention action can proceed, and notifies the administrator. The retention action is held until the linked matter is resolved and the administrator clears the hold
- **Postcondition:** Personal data that has exceeded its configured retention period has been anonymised or deleted; the action is logged; records with active dependencies are held for manual review rather than processed automatically

---

### UC-060: Generate Annual Data Audit Report

- **Related Requirement:** FR-060
- **Actor:** System Administrator, Data Protection Officer
- **Precondition:** The system holds warranty and customer data; the reporting period has reached the annual audit milestone
- **Main Flow:**
  1. Administrator navigates to the Data Management section of the admin interface
  2. Administrator selects "Generate Annual Data Audit Report"
  3. The system compiles a report covering: all categories of data held, the volume of records in each category, the age distribution of records, the retention status of each category (within retention window, approaching retention limit, past retention limit), and a summary of retention actions taken in the previous year
  4. The system generates the report in a downloadable format
  5. Administrator reviews and forwards the report to the Data Protection Officer and Legal team
- **Alternative Flow:** The retention configuration has not been fully set up for all data categories — for example, a new data category was added but its retention period was not configured. The system generates the report with a flag indicating that one or more data categories have no configured retention policy, and identifies those categories. The administrator is prompted to configure the missing policies before the next retention cycle
- **Postcondition:** An annual data audit report has been generated showing the completeness, age, and retention status of all personal data held in the system

---

### UC-061: Log All Access to Customer Personally Identifiable Information

- **Related Requirement:** FR-061
- **Actor:** System (automated, triggered on every PII access event)
- **Precondition:** An authenticated internal user has accessed a screen or record containing customer PII — such as name, email, address, or phone number
- **Main Flow:**
  1. An internal user opens a customer profile or a claim record containing customer PII
  2. The system immediately creates an access log entry recording: the user ID, their role, the timestamp of access, the type of action (read), and the specific record accessed
  3. The log entry is written to the tamper-resistant audit log
  4. The access event is not shown to the user and does not interrupt their workflow
  5. The log is available to the IT Security Officer and designated compliance staff for review
- **Alternative Flow:** An automated system process accesses PII — for example, a notification job reading a customer's email address to send an expiry reminder. The system logs the access event with the system process identified as the actor rather than a user ID, and the triggering event (e.g. expiry notification job run) referenced in the log entry
- **Postcondition:** Every access to customer PII — by any user or system process — has been logged with a timestamp, actor identity, and record identifier in the tamper-resistant audit log

---

### UC-062: Protect Audit Logs from Modification

- **Related Requirement:** FR-062
- **Actor:** System (storage enforcement), IT Security Officer (audit)
- **Precondition:** Audit log entries exist in the system; a user or process attempts to modify or delete a log entry
- **Main Flow:**
  1. Audit log entries are written to a write-protected, tamper-resistant storage location at the point of creation
  2. Authorised users — such as the IT Security Officer and compliance staff — can read and export audit log entries
  3. No user role — including System Administrator — has the ability to modify or delete existing audit log entries through any interface or API
  4. The IT Security Officer periodically reviews audit logs for anomalies and exports records as required for compliance reviews
- **Alternative Flow:** A System Administrator attempts to delete an audit log entry — for example, believing it was created in error. The system denies the action and displays a message confirming that audit log entries cannot be modified or deleted. The attempted deletion is itself logged as a security event and an alert is sent to the IT Security Officer
- **Postcondition:** The integrity of all audit log entries is guaranteed; no actor within the system can alter historical log records; all attempts to modify logs are themselves logged and alerted

---

### UC-063: Alert Administrator If Audit Logging Fails

- **Related Requirement:** FR-063
- **Actor:** System (automated monitoring)
- **Precondition:** The audit logging function is active and monitoring its own operational status
- **Main Flow:**
  1. The system continuously monitors the operational status of the audit logging service
  2. The audit logging service processes and records events normally
  3. The system confirms logging is functional as part of its routine health monitoring
- **Alternative Flow — Logging failure detected:** The audit logging service becomes unavailable — for example, due to a storage capacity issue or a service connectivity failure. The system detects the logging failure within the configured monitoring interval. An immediate alert is sent to the System Administrator and the IT Security Officer. The alert describes the nature of the failure and the time at which logging was last confirmed active. The system may optionally queue log events locally until the logging service is restored, to avoid gap in coverage
- **Postcondition:** Any failure of the audit logging function is detected and escalated immediately; the period during which logging was unavailable is documented; no logging failures go undetected

---

### UC-064: Submit and Fulfil Subject Access Request

- **Related Requirement:** FR-064
- **Actor:** Customer (submission), System Administrator (fulfilment)
- **Precondition:** Customer is logged into the customer portal; the customer wishes to receive a copy of all personal data held about them
- **Main Flow:**
  1. Customer navigates to the Privacy section of their account settings and selects "Request My Data"
  2. The system records the Subject Access Request (SAR) with the date, the customer's identity, and a unique SAR reference number
  3. The request is surfaced to the System Administrator in the admin interface with a statutory response deadline displayed (default: 30 days from submission)
  4. Administrator navigates to the SAR management interface, selects the request, and initiates the data compilation
  5. The system automatically compiles all personal data held for that customer across all modules: account details, warranty registrations, claims, uploaded documents, communications, consent records
  6. The compiled data is generated as a structured, downloadable export
  7. Administrator reviews the export and delivers it to the customer through the portal or via secure email
  8. The SAR is marked as fulfilled and the fulfilment date is logged
- **Alternative Flow:** The customer submits a SAR but the data compilation reveals records held in an older data migration that are not fully linked to the customer's system account. The administrator is alerted to the discrepancy and prompted to manually review and supplement the automated compilation before delivering the response. The SAR deadline counter continues running
- **Postcondition:** The customer has received all personal data held about them in a structured, accessible format within the statutory deadline; the SAR is logged as fulfilled with a timestamp

---

### UC-065: Process Customer Data Deletion Request

- **Related Requirement:** FR-065
- **Actor:** Customer (submission), System (automated processing)
- **Precondition:** Customer is logged into the portal and has submitted a request to have their personal data deleted
- **Main Flow:**
  1. Customer navigates to the Privacy section of their account settings and selects "Delete My Data"
  2. The system records the deletion request with a timestamp
  3. The system evaluates whether the request can be fulfilled automatically — checking for active open claims, statutory retention obligations, or other legitimate grounds to retain the data
  4. If no retention hold applies, the system automatically anonymises the customer's PII across all records — replacing identifiable fields with anonymised values while preserving statistical and warranty records for compliance purposes
  5. The customer receives a confirmation that their data deletion request has been processed
  6. The deletion action is logged in the audit trail
- **Alternative Flow:** The customer has an open active claim at the time of the deletion request. The system cannot delete the data because it is required for the ongoing contractual obligation. The system automatically flags the request as "Pending — Active Claim" and notifies the customer that their data will be retained until the claim is resolved, after which the deletion will be processed. The customer is given an expected timeframe
- **Postcondition:** Customer personal data has been anonymised or deleted in accordance with the applicable retention policy; the action — or the reason for deferral — is logged in the audit trail

---

### UC-066: Display Warranty Terms During Registration

- **Related Requirement:** FR-066
- **Actor:** Customer
- **Precondition:** Customer is completing the warranty registration form; warranty terms are configured in the system for the relevant product
- **Main Flow:**
  1. Customer completes the product and purchase details on the registration form
  2. Before reaching the final submission step, the system presents the applicable warranty terms and conditions in full — either inline on the page or in a clearly accessible modal window
  3. Customer reads the terms, which are displayed in plain language with a clear structure
  4. The page provides a "Save as PDF" or "Print" button so the customer can retain a copy of the terms for their records
  5. Customer acknowledges that they have had the opportunity to read the terms and proceeds to submit the registration
- **Alternative Flow:** Customer clicks Submit without having scrolled through or acknowledged the terms. The system requires an acknowledgement action — such as ticking a checkbox confirming the customer has read the terms — before the submission is accepted. The terms remain visible and accessible on the page
- **Postcondition:** The customer has been presented with the full, applicable warranty terms and had the opportunity to save or print them; an acknowledgement is recorded on the registration

---

### UC-067: Record Warranty Terms Version Accepted at Registration

- **Related Requirement:** FR-067
- **Actor:** System (automated, triggered at registration submission)
- **Precondition:** The customer has completed the registration form and the applicable warranty terms version has been determined by the system
- **Main Flow:**
  1. Customer submits the completed registration form
  2. The system identifies the version of the warranty terms that was presented to the customer during the registration session
  3. The system stores the terms version identifier permanently on the registration record as an immutable field
  4. The stored version can be retrieved at any time to confirm exactly what terms the customer was shown and accepted at registration
  5. The terms version reference is visible to authorised internal users when viewing the registration record
- **Alternative Flow:** The system cannot identify a current terms version for the product — for example, because no terms have been configured for a newly added product. The system prevents the registration from being completed and prompts the administrator to configure the warranty terms for that product before registration can proceed
- **Postcondition:** The registration record contains a permanent, immutable reference to the exact version of warranty terms that was presented to the customer at the time of registration

---

### UC-068: Apply Structured Rejection Reason Codes

- **Related Requirement:** FR-068
- **Actor:** Claims Processor
- **Precondition:** A claim is being rejected; the processor has selected the Reject action; rejection reason codes have been configured in the system by an administrator
- **Main Flow:**
  1. Processor selects the Reject action on a claim
  2. The system presents a mandatory rejection reason field containing a dropdown list of configured rejection reason codes (e.g. Warranty Expired, Fault Excluded Under Terms, Insufficient Proof of Purchase, Serial Number Not Recognised)
  3. Processor selects the most applicable code from the list
  4. Processor optionally adds supplementary free-text notes to support the coded reason
  5. Processor confirms the rejection
  6. The rejection reason code is stored on the claim record and is included in the rejection notification sent to the customer
- **Alternative Flow:** None of the available rejection reason codes accurately describes the basis for rejection. Processor selects the closest available code and uses the free-text notes field to document the specific circumstances. The processor flags the gap to the administrator to consider adding a new code to the controlled vocabulary. The claim is rejected with the closest available code
- **Postcondition:** The rejection reason is recorded as a structured code on the claim record, enabling accurate reporting on rejection trends; the customer is informed of the specific reason for rejection

---

### UC-069: Resubmit a Claim After Rejection

- **Related Requirement:** FR-069
- **Actor:** Customer
- **Precondition:** A claim has been rejected; the customer believes the rejection was incorrect or has new information to provide; the original warranty is still within its applicable period
- **Main Flow:**
  1. Customer logs into the portal and navigates to their claim history
  2. Customer selects the rejected claim and reviews the rejection reason
  3. Customer selects "Resubmit Claim" from the rejected claim record
  4. The system creates a new claim record pre-populated with the details from the original claim and linked to the original by a parent-child reference
  5. Customer updates the relevant fields — for example, uploading a clearer proof of purchase — and submits
  6. The new claim enters the standard claim workflow from the Submitted state and is visible to processors alongside its parent claim history
- **Alternative Flow:** Customer attempts to resubmit a claim for a warranty that has since expired. The system informs the customer that while the original claim was submitted within the warranty period, the warranty has since expired and resubmission is not permitted. The customer is directed to contact customer service to discuss options
- **Postcondition:** A new, linked claim record exists in the system referencing the original rejected claim; the full history of both the original rejection and the resubmission is visible to processors

---

### UC-070: Enforce Per-Serial-Number Claim Limit

- **Related Requirement:** FR-070
- **Actor:** Customer, System
- **Precondition:** A customer is attempting to submit a claim; a configured limit exists on the number of active claims per serial number within a single warranty period
- **Main Flow:**
  1. Customer submits a new claim for a product
  2. The system checks the number of active or closed claims associated with that serial number within the current warranty period
  3. The claim count is within the configured limit and the submission proceeds normally
- **Alternative Flow — Limit exceeded:** The claim count for that serial number meets or exceeds the configured limit. The system does not automatically reject the submission but instead flags it for manual review, creating the claim in a "Pending Review — Claim Limit Reached" state. The processor is alerted to review the claim history for that serial number before proceeding. The customer receives an acknowledgement confirmation but is informed that their claim is subject to additional review
- **Postcondition:** Claims that exceed the configured per-serial-number limit are flagged for manual review rather than auto-rejected, allowing the processor to make an informed decision while the system prevents the limit from being silently bypassed

---

### UC-071: Track Warranty Registration Rate Metric

- **Related Requirement:** FR-071
- **Actor:** Business Sponsor, Warranty Operations Manager, Marketing Manager
- **Precondition:** Warranty registration data exists in the system; sales volume data has been imported or manually entered for the relevant products and time periods
- **Main Flow:**
  1. User navigates to the reporting or dashboard module
  2. User selects the Registration Rate report or dashboard widget
  3. User applies filters for product line and time period
  4. The system calculates the registration rate: total registrations divided by total units sold for the selected scope, expressed as a percentage
  5. The system displays the registration rate metric alongside registration volumes and units sold, with trend data over the selected period
- **Alternative Flow:** Sales volume data has not been entered for the selected product or time period. The system displays the registration volume but is unable to calculate a registration rate, and displays a message indicating that the rate cannot be computed without the corresponding sales data. An administrator prompt is shown to update the sales volume figures
- **Postcondition:** Authorised users can view the warranty registration rate as a percentage of units sold, filterable by product and time period, to track progress toward the 60% registration target

---

### UC-072: Record Registration Channel on Registration Record

- **Related Requirement:** FR-072
- **Actor:** System (automated, set at point of registration creation)
- **Precondition:** A warranty registration is being created — either by the customer through the self-service portal, by a CSR on behalf of a customer, or through a batch import from a retailer
- **Main Flow:**
  1. A warranty registration is created through any channel
  2. The system automatically detects or is informed of the channel through which the registration was created: self-registered online (customer), submitted by CSR on behalf of customer, or imported from retailer data
  3. The channel is recorded as a structured field on the registration record at the point of creation
  4. The registration channel is available as a filter dimension in the reporting module
- **Alternative Flow:** A registration is created through a channel that does not map to one of the configured channel types — for example, a legacy migration of historical records. The system assigns a default channel value of "Unknown — Legacy Migration" and flags the record for administrative review
- **Postcondition:** Every registration record in the system has a recorded channel field, enabling analysis of which registration pathways are most effective

---

### UC-073: Record Time-to-Registration Timestamp

- **Related Requirement:** FR-073
- **Actor:** System (automated, recorded at registration)
- **Precondition:** A warranty registration has been submitted; the purchase date is recorded on the registration
- **Main Flow:**
  1. Customer submits a warranty registration with a purchase date recorded
  2. The system records the registration submission timestamp
  3. The system calculates the number of days between the purchase date and the registration date
  4. This time-to-registration value is stored as a derived field on the registration record
  5. The field is available as a reporting dimension for customer segmentation analysis
- **Alternative Flow:** The purchase date entered by the customer appears to be in the future or is implausible relative to the registration date. The system flags the registration for review and records the time-to-registration as a negative or anomalous value rather than silently accepting it
- **Postcondition:** Every registration record contains a time-to-registration field that can be used for segmentation analysis — for example, identifying customers who registered within 7 days of purchase versus those who registered much later

---

### UC-074: Broadcast Processing Hold Notification to All Processors

- **Related Requirement:** FR-074
- **Actor:** System (triggered by administrator action), Claims Processor (acknowledgement)
- **Precondition:** A processing hold has been created by an administrator for one or more products or categories; processors are active in the system
- **Main Flow:**
  1. Administrator creates a processing hold specifying the affected products and reason
  2. The system immediately sends a broadcast notification to all active claims processors currently logged in, displayed as a prominent alert in their interface
  3. The notification explains the affected products and the reason for the hold
  4. Each processor is required to click an acknowledgement button before the alert is dismissed
  5. The system records the timestamp of acknowledgement for each processor
  6. Processors who are not logged in at the time receive the notification the next time they log in, with the same acknowledgement requirement before they can access their queue
- **Alternative Flow:** A processor dismisses or closes the alert window without formally acknowledging it. The system re-presents the unacknowledged hold notification the next time the processor attempts to open an affected claim, ensuring the hold instruction cannot be bypassed through accidental dismissal
- **Postcondition:** All active and subsequent-login processors have received, read, and formally acknowledged the processing hold notification; acknowledgements are recorded in the audit log

---

### UC-075: Configure Warranty Duration Rules via Admin Interface

- **Related Requirement:** FR-075
- **Actor:** System Administrator
- **Precondition:** System Administrator is logged in; a warranty duration rule needs to be created or updated for a product category — for example, updating premium appliances from 24 months to 36 months
- **Main Flow:**
  1. Administrator navigates to the Warranty Rules section of the admin interface
  2. Administrator selects the product category to be updated
  3. The system presents the current warranty duration configuration for that category
  4. Administrator updates the warranty duration value (e.g. changes from 24 to 36 months)
  5. Administrator sets an effective date for the change — new registrations from that date forward will use the updated duration
  6. Administrator saves the change
  7. The system logs the configuration change in the admin audit trail with the user's identity and timestamp
- **Alternative Flow:** Administrator attempts to set a warranty duration of zero months or enters a non-numeric value. The system validates the input and displays an error message preventing the save. The existing duration rule remains unchanged
- **Postcondition:** The warranty duration rule for the selected product category has been updated; existing registrations are unaffected; new registrations from the effective date will use the updated duration; no developer involvement was required

---

### UC-076: Capture Retailer from Controlled List

- **Related Requirement:** FR-076
- **Actor:** Customer (during registration), System Administrator (maintaining retailer list)
- **Precondition:** The warranty registration form is open; the administrator has configured a controlled retailer list in the system
- **Main Flow:**
  1. Customer reaches the Retailer field on the registration form
  2. The system presents a searchable dropdown list of configured retailers
  3. Customer types the first few letters of the retailer name; the list filters to matching options
  4. Customer selects their retailer from the list
  5. The structured retailer identifier is stored on the registration record
- **Alternative Flow:** Customer cannot find their retailer in the list — for example, the product was purchased from a smaller independent retailer not yet in the system. Customer selects "Other" from the list and enters the retailer name in a free-text field. The registration proceeds and the free-text retailer name is stored with a flag for administrator review, so the administrator can consider adding the retailer to the controlled list
- **Postcondition:** The registration record contains a structured retailer identifier from a controlled vocabulary, enabling retailer-level analysis and reporting; unknown retailers are captured for administrator review rather than silently accepted as unstructured data

---

### UC-077: Apply Organisation Brand Identity to Customer Portal

- **Related Requirement:** FR-077
- **Actor:** System Administrator, Marketing Manager (brand approval)
- **Precondition:** Brand identity guidelines have been provided by the Marketing Manager; the customer portal has been configured with the organisation's subdomain
- **Main Flow:**
  1. The customer portal is accessible at the organisation's configured subdomain (e.g. warranty.organisationname.com)
  2. All customer-facing screens display the organisation's brand colour palette, typography, and logo in accordance with the brand guidelines
  3. All outbound customer communications — registration confirmations, claim acknowledgements, expiry reminders — use the configured brand identity and tone of voice
  4. Marketing Manager reviews the portal design against brand guidelines during UAT and confirms approval before go-live
  5. Any subsequent brand updates are applied by the administrator through the brand configuration settings without requiring a development change
- **Alternative Flow:** The brand guidelines include a typography font that is not available as a standard web font and requires a licensed web font integration. The development team identifies the dependency during design review and resolves it during build. The Marketing Manager re-confirms approval after the resolution is implemented
- **Postcondition:** All customer-facing elements of the portal and communications reflect the organisation's brand identity as approved by the Marketing Manager; the portal is served from the organisation's own subdomain

---

### UC-078: Investigate User or Claim Issues via Admin Panel

- **Related Requirement:** FR-078
- **Actor:** System Administrator
- **Precondition:** A user or customer has reported a problem — for example, a failed registration, a claim record that appears to be missing, or an error during login; the System Administrator is logged in
- **Main Flow:**
  1. Administrator navigates to the Admin Investigation Panel
  2. Administrator searches for the affected user account or claim record using the email address, claim reference number, or serial number
  3. The system retrieves and displays the record with full detail: account status, recent activity log, any error events associated with the record, and associated notifications
  4. Administrator reviews the information and identifies the cause of the issue
  5. Administrator takes the appropriate corrective action — for example, manually re-triggering a failed notification or unlocking an account
  6. Administrator records a note on the record documenting the investigation and action taken
- **Alternative Flow:** The Administrator's search returns no results — the user or claim record cannot be found. The Administrator uses alternative search fields (e.g. serial number instead of email) to locate the record. If the record genuinely does not exist, the Administrator logs the investigation outcome and advises the reporter accordingly
- **Postcondition:** The System Administrator has been able to investigate and resolve a user-reported issue without requiring direct database access or developer escalation; the investigation and action are logged

---

### UC-079: Receive Daily System Health Report

- **Related Requirement:** FR-079
- **Actor:** System Administrator
- **Precondition:** The daily health report job is configured and active; the previous day's system activity data is available
- **Main Flow:**
  1. The system runs the daily health report job at the configured time each morning
  2. The report compiles key system health indicators from the previous 24 hours: uptime percentage, error rates by module, notification queue depth and delivery success rate, number of failed jobs, user login activity, and any active security alerts
  3. The system emails the health report to the System Administrator
  4. The report is also archived in the admin interface for reference
- **Alternative Flow:** The health report job itself fails to run — for example, due to a server issue overnight. The monitoring system detects that no health report has been generated by the expected time and sends an alert to the System Administrator, ensuring they are aware that the health report is missing rather than assuming "no report means no issues"
- **Postcondition:** The System Administrator receives a daily summary of system health indicators, providing a baseline for identifying emerging issues before they become user-impacting incidents

---

### UC-080: Alert Administrator to Notification Queue Depth Issues

- **Related Requirement:** FR-080
- **Actor:** System (automated monitoring)
- **Precondition:** Notification queue depth monitoring is active; a configurable alert threshold is set for notification delivery queue depth
- **Main Flow:**
  1. The system monitors the notification delivery queue depth continuously
  2. The queue depth is within normal operating parameters
  3. The system logs the queue status as healthy in the monitoring log
- **Alternative Flow — Queue depth threshold breached:** The notification queue depth exceeds the configured threshold — for example, a large batch of expiry reminders has been queued but the delivery service is processing them slowly. The system immediately generates an alert and sends it to the System Administrator. The alert message includes the current queue depth, the threshold, the type of notifications queued, and the estimated time to clear at current processing rate. The administrator investigates and takes corrective action before customers begin reporting missing communications
- **Postcondition:** The System Administrator is alerted to notification queue depth issues before they result in delayed or missed customer communications; the alert is logged in the system monitoring record

---

### UC-081: Set a Follow-Up Reminder on a Claim

- **Related Requirement:** FR-081
- **Actor:** Customer Service Representative
- **Precondition:** A CSR is viewing a claim record and has made a commitment to the customer — for example, that someone will follow up within 48 hours; the CSR is logged in with a CSR role
- **Main Flow:**
  1. CSR opens the relevant claim record after concluding a customer interaction
  2. CSR selects "Set Follow-Up Reminder" from the claim action menu
  3. The system presents a reminder date/time picker and a notes field
  4. CSR sets the reminder for the appropriate date and time and enters a brief note describing the follow-up action required
  5. CSR saves the reminder
  6. At the configured date and time, the system surfaces the reminder in the CSR's task view, displaying the claim reference and the note
- **Alternative Flow:** The CSR sets a reminder but is absent from work on the reminder date. The system displays the overdue reminder to the CSR's team supervisor after the reminder has been unacknowledged for a configurable grace period, so that the follow-up is not missed entirely
- **Postcondition:** The follow-up reminder is linked to the claim record and will surface at the configured time; the CSR's commitment to the customer is tracked in the system rather than in a personal notebook

---

### UC-082: Capture and Use Customer Preferred Contact Channel

- **Related Requirement:** FR-082
- **Actor:** Customer
- **Precondition:** Customer is completing or has completed the warranty registration form; a preferred contact channel field is available
- **Main Flow:**
  1. During registration, the customer is presented with an optional "Preferred Contact Channel" field with options: email, SMS, phone
  2. Customer selects their preferred channel
  3. The preference is stored on the customer's account record
  4. When the system generates an outbound communication — expiry reminder, claim status update, acknowledgement — it routes the notification to the customer's preferred channel
- **Alternative Flow:** Customer selects SMS as their preferred channel but has not provided a phone number on their account. The system defaults to email delivery for notifications and displays a prompt in the customer's portal account noting that SMS preference has been set but a phone number is required to activate it, with a link to update their contact details
- **Postcondition:** Customer's preferred contact channel is stored and used to route outbound warranty communications; where a preference cannot be fulfilled, the system falls back to email and prompts the customer to complete their contact details

---

### UC-083: Capture Optional Usage Context at Registration

- **Related Requirement:** FR-083
- **Actor:** Customer
- **Precondition:** Customer is completing the warranty registration form; the optional usage context section is configured and visible on the form
- **Main Flow:**
  1. Customer completes the mandatory registration fields
  2. The customer is presented with a clearly labelled optional section — separate from mandatory fields — containing questions such as: "How will you primarily use this product?" (domestic / commercial) and "How many people are in your household?"
  3. The section includes a brief explanation of why the information is being requested and confirms it is optional
  4. Customer completes or skips the optional fields and submits the form
  5. Any provided values are stored on the registration record and are available for product and marketing analysis
- **Alternative Flow:** Customer skips the optional section entirely. The system accepts the submission with null values in the optional fields and proceeds normally. Reports that use these fields display the data as "Not Provided" for records where no value was captured
- **Postcondition:** Optional usage context data has been captured where customers chose to provide it; the registration process is not impeded for customers who skip the optional section

---

### UC-084: Flag Claims Indicating Commercial Product Use

- **Related Requirement:** FR-084
- **Actor:** System (automated, triggered when reviewing usage context against warranty terms)
- **Precondition:** A warranty claim has been submitted; the registration record for the claimed product contains a usage context field indicating commercial use; the applicable warranty terms exclude commercial use
- **Main Flow:**
  1. Processor opens a claim for review
  2. The system checks the usage context recorded on the linked warranty registration
  3. The usage context indicates commercial use
  4. The system checks the applicable warranty terms version and identifies that commercial use is listed as an exclusion
  5. A flag is displayed prominently in the claim record alerting the processor to the potential commercial use exclusion, with a reference to the relevant terms clause
  6. The processor reviews the flag, investigates, and makes an informed decision about the claim
- **Alternative Flow:** The usage context field on the registration is blank — the customer did not complete the optional field. The system does not flag the claim for commercial use exclusion, as there is no evidence to support the flag. The processor may still choose to query commercial use if other evidence in the claim suggests it
- **Postcondition:** Claims where the registration record indicates commercial use of a product covered by terms that exclude commercial use are automatically flagged for processor attention; processors are not required to manually cross-reference usage context and warranty terms

---

### UC-085: Record Geographic Region on Registration

- **Related Requirement:** FR-085
- **Actor:** System (automated, triggered at registration)
- **Precondition:** Customer has provided a postal address during registration; a geographic region mapping has been configured in the system
- **Main Flow:**
  1. Customer completes the registration form including their postal address
  2. The system derives the customer's geographic region from their postal address using the configured regional mapping (e.g. mapping postcode areas to defined regions)
  3. The derived region is stored as a structured field on the registration record
  4. The region field is available as a reporting and analysis dimension
- **Alternative Flow:** The customer's postal address cannot be mapped to a defined region — for example, the postcode is incomplete or does not match the configured mapping. The system stores the region field as "Unassigned" and flags the record for administrator review. The registration proceeds normally
- **Postcondition:** Every registration record contains a structured geographic region field derived from the customer's address, enabling regional analysis of warranty and claims data

---

### UC-086: Report Claim Rates by Geographic Region

- **Related Requirement:** FR-086
- **Actor:** Data Analyst, Product Manager
- **Precondition:** Registration records contain geographic region fields; claims exist in the system linked to those registrations; the reporting module is accessible to the user
- **Main Flow:**
  1. User navigates to the reporting module and selects the Regional Claims Analysis report
  2. User applies filters for product category and time period
  3. The system retrieves claims data, joins it to the registration records' region fields, and calculates claim rates per region
  4. The system displays the results as a summary table showing claim volumes and claim rates by region
  5. User exports the report or uses it to identify regions with anomalously high claim rates for further investigation
- **Alternative Flow:** A significant proportion of registration records have "Unassigned" in the region field — for example, because historical data was migrated without address information. The report displays the "Unassigned" bucket prominently in the results and the user is informed that the regional analysis may not be fully representative until the unassigned records are resolved
- **Postcondition:** The user has a regional breakdown of warranty claim rates that can be used to identify geographic patterns in product faults or claims behaviour

---

### UC-087: Manage System Configuration via Admin Interface

- **Related Requirement:** FR-087
- **Actor:** System Administrator
- **Precondition:** System Administrator is logged in; a configuration change is required — for example, a new rejection reason code needs to be added, or an SLA threshold needs to be updated
- **Main Flow:**
  1. Administrator navigates to the System Configuration section of the admin interface
  2. The configuration area is organised by category: Warranty Rules, Notification Templates, Rejection Reason Codes, Retailer List, Product Categories, SLA Thresholds
  3. Administrator selects the relevant category and item to update
  4. Administrator makes the required change — for example, adding a new rejection code "Claim Submitted After Product Registration Window Expired"
  5. Administrator saves the change
  6. The new configuration is immediately active across the system
  7. All configuration changes are logged in the admin audit trail
- **Alternative Flow:** Administrator attempts to delete a rejection reason code that is currently referenced by one or more existing claim records. The system prevents the deletion and warns the administrator that the code is in use. The administrator may deactivate the code — making it unavailable for future claims while preserving its reference in existing records — rather than deleting it
- **Postcondition:** The system configuration change is live immediately; no developer deployment was required; the change is recorded in the admin audit trail with the administrator's identity and timestamp

---

### UC-088: Log Configuration Changes in Admin Audit Trail

- **Related Requirement:** FR-088
- **Actor:** System (automated, triggered on every admin configuration change)
- **Precondition:** An administrator has made a configuration change through the admin interface
- **Main Flow:**
  1. Administrator makes a configuration change — for example, updating the SLA threshold from 5 to 3 business days
  2. Administrator saves the change
  3. The system immediately creates an admin audit log entry recording: the administrator's user ID, their role, the timestamp of the change, the category of configuration changed, the previous value, and the new value
  4. The audit entry is immutable and is visible to the IT Security Officer and designated compliance reviewers
  5. A summary of recent admin configuration changes is accessible from the admin interface for management review
- **Alternative Flow:** The configuration change is made by an automated system process — for example, an automated data retention policy update — rather than a human administrator. The system logs the change with the system process identified as the actor and the triggering rule referenced in the log entry
- **Postcondition:** Every configuration change made through the admin interface is permanently logged with full details of who made the change, what was changed, and when; the log provides a complete history of system configuration evolution

---

### UC-089: Log Scheduled Job Execution and Alert on Failure

- **Related Requirement:** FR-089
- **Actor:** System (automated, running scheduled jobs)
- **Precondition:** Scheduled automated jobs are configured in the system — including notification dispatch, data retention actions, and export tasks
- **Main Flow:**
  1. A scheduled job runs at its configured time — for example, the nightly notification dispatch job
  2. The job completes successfully, processing all pending notifications
  3. The system logs the job execution with: job name, start time, end time, number of records processed, and a success status
  4. The log entry is accessible in the admin interface and is included in the daily health report
- **Alternative Flow — Job failure:** The scheduled job encounters an error during execution — for example, the notification service is unavailable. The job halts and logs a failure record with the error details, the point of failure, and the number of records processed before the failure. The system immediately sends an alert to the System Administrator identifying the failed job, the error type, and the impact. The administrator investigates and either resolves the root cause and re-triggers the job manually, or acknowledges the failure for the next scheduled run
- **Postcondition:** All scheduled job executions are logged with their outcome; failures are detected and escalated immediately rather than silently missed; the administrator has a complete record of automated job history for troubleshooting

---

### UC-090: Generate Annual Insurance Underwriter Report

- **Related Requirement:** FR-090
- **Actor:** Data Analyst, Legal / Compliance Officer
- **Precondition:** The system holds a full year of warranty and claims data; the user is logged in with reporting access; the annual reporting period has been reached
- **Main Flow:**
  1. User navigates to the reporting module and selects the "Annual Warranty Claims Summary — Underwriter Report"
  2. User selects the reporting year
  3. The system retrieves all claims for that calendar year and aggregates them by product category
  4. The report is generated showing: claim volumes by product category, average resolution time per category, claim approval rates, rejection rates, and rejection reason breakdowns
  5. The report includes a timestamp and a system-generated certification that the data has been extracted directly from the live system record with no manual modification
  6. User exports the report in the required format (PDF or structured document) for submission to the insurance underwriter
- **Alternative Flow:** The user generates the report and discovers that a data quality issue — such as a large volume of uncategorised claims — is affecting the completeness of the product category breakdown. The system surfaces a data quality warning alongside the report, quantifying the number of records that could not be categorised and the potential impact on the reported figures. The user is able to investigate and resolve the categorisation issue before finalising the report for submission
- **Postcondition:** A complete, reproducible annual warranty claims summary report has been generated directly from system data, suitable for submission to the insurance underwriter; the report can be regenerated at any future point using the point-in-time query function to produce an identical result

---

> **All 90 Use Cases completed** — one for every Functional Requirement from FR-001 through FR-090. Each use case includes a named actor, stated precondition, minimum 4-step main flow, a realistic alternative flow covering the most likely failure or exception scenario, and a defined postcondition. This concludes the Elicitation phase. Ready for your next instruction.
