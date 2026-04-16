## Use Cases

### UC-001: Register Customer Account

- **Actor:** Customer
- **Precondition:** The customer does not have an existing account; the system is accessible
- **Main Flow:**
  1. The customer opens the application and selects "Create Account"
  2. The customer chooses to register using email or via a Google/Facebook account
  3. If registering by email, the customer enters their full name, email address, phone number, and a password meeting complexity requirements
  4. The customer reads and explicitly accepts the privacy policy and terms of use
  5. The system validates all entered fields and checks that the email address is not already registered
  6. The system creates the account and sends an email verification link to the customer
  7. The customer clicks the verification link within the expiry period
  8. The system confirms the account is active and redirects the customer to the home screen
  9. The customer selects their preferred language (English or Khmer) on first login
- **Alternative Flow:** If the customer selects social login (Google or Facebook), the system redirects to the OAuth provider; upon successful authorisation the system creates the account using the provider's name and email, skipping manual data entry and email verification
- **Exception Flow:** If the email address is already registered, the system displays an error and suggests the customer log in or use password reset; if the email verification link expires before being used, the system allows the customer to request a new verification link
- **Postcondition:** A verified customer account exists; the customer's consent record is stored with timestamp and policy version; the customer can log in and access the system
- **Related Requirements:** FR-001, FR-002, FR-003, FR-041, FR-079, FR-090

---

### UC-002: Customer Login & Session Management

- **Actor:** Customer; Internal Staff
- **Precondition:** The user has a registered and verified account
- **Main Flow:**
  1. The user opens the application and enters their email address and password
  2. The system validates the credentials against the stored record
  3. For internal staff accounts, the system prompts for a two-factor authentication code (email OTP or authenticator app)
  4. The staff member enters the OTP code
  5. The system issues a secure session token stored as an HTTP-only cookie
  6. The system logs the successful login event with timestamp and IP address
  7. The user is directed to their role-appropriate home screen
  8. The session remains active while the user is engaged; after 30 minutes of inactivity the system automatically logs the user out
  9. Upon logout, the session token is invalidated server-side
- **Alternative Flow:** If the customer has forgotten their password, they select "Forgot Password"; the system sends a time-limited reset link to the registered email; the customer sets a new password meeting complexity rules and is returned to the login screen
- **Exception Flow:** If the user enters incorrect credentials five consecutive times, the account is locked for 15 minutes and the user is notified by email; if the two-factor code is invalid or expired, the system rejects the login attempt and allows the user to request a new code
- **Postcondition:** The user is authenticated with an active session; all login events are recorded in the security audit log; the session expires after inactivity or logout
- **Related Requirements:** FR-002, FR-004, FR-046, FR-047, FR-071

---

### UC-003: Register Product Warranty

- **Actor:** Customer
- **Precondition:** The customer is logged in; the product model to be registered exists in the product catalogue; the customer has their purchase invoice available
- **Main Flow:**
  1. The customer navigates to "Register New Warranty" from the home screen
  2. The system displays the warranty coverage terms for the selected product, requiring the customer to acknowledge before proceeding
  3. The customer selects the product category and product model from the catalogue, or scans the product QR code to pre-fill these fields
  4. The customer enters the product serial number, either by typing or scanning the serial label using the device camera
  5. The customer enters the purchase date and retailer name; the retailer name is validated against the list of authorised retailers
  6. The customer uploads a photo of their purchase invoice; the system validates the file format, size (max 10 MB), and minimum image resolution
  7. The customer uploads a photo of the product's serial number label
  8. The system validates the serial number format against the rules defined for the selected product model and confirms the purchase date is not in the future
  9. The customer reviews the registration summary and submits
  10. The system creates the warranty record, assigns it Pending status, automatically calculates the warranty expiry date, and displays a confirmation screen with a reference number
  11. The system prompts the customer to optionally rate their registration experience (1–5 stars)
  12. For multiple products, the customer can repeat from step 3 without re-entering account details
- **Alternative Flow:** If the customer begins a registration but cannot complete it in one session, the system auto-saves the draft every 60 seconds; the customer can resume the draft registration from the home screen at a later time
- **Exception Flow:** If the uploaded image fails the resolution check or is corrupted, the system rejects the file and displays a descriptive error explaining what is wrong and how to correct it; if the serial number format does not match the selected product model's rules, the system highlights the field and prompts the customer to verify the number against their product
- **Postcondition:** A warranty registration record exists in Pending status; the expiry date is calculated and stored; a submission confirmation is displayed and the record is visible in the customer's warranty list
- **Related Requirements:** FR-005a, FR-005b, FR-006, FR-007, FR-009, FR-010, FR-052, FR-053, FR-069, FR-076, FR-085, FR-086

---

### UC-004: Review & Approve Warranty Registration

- **Actor:** Warranty Registration Officer
- **Precondition:** One or more warranty registrations are in Pending status in the review queue; the officer is logged in with the correct role
- **Main Flow:**
  1. The officer navigates to the registration review queue, which lists all pending registrations in order of submission date
  2. The officer selects a registration to review
  3. The system displays full registration details including customer information, product model, serial number, purchase date, retailer name, uploaded invoice image, and serial label image
  4. The officer reviews the submitted documents and verifies that the serial number, purchase date, and retailer are valid
  5. The officer views the warranty status change history for the record
  6. The officer approves the registration; the system updates the warranty status to Active and records the officer's ID and timestamp
  7. The system sends an approval notification (in-app and email) to the customer within 5 minutes
  8. The officer continues to the next pending registration in the queue
- **Alternative Flow:** If the officer determines that the registration is invalid (e.g. unclear invoice, unrecognised serial number), the officer selects Reject and must enter a mandatory rejection reason; the system updates the status to Rejected and notifies the customer with the reason; the customer may resubmit after making corrections
- **Exception Flow:** If the officer needs to manually deactivate an already Active warranty (e.g. due to fraud detection), the officer selects Deactivate, enters a mandatory reason, and the system records the action in the audit log and notifies the customer; if two officers attempt to update the same record simultaneously, the system uses optimistic locking to detect the conflict and displays a warning to the second officer
- **Postcondition:** The warranty registration status is updated to Active or Rejected; the customer is notified of the outcome; the status change is recorded in the warranty history audit trail
- **Related Requirements:** FR-008, FR-009, FR-010, FR-015, FR-050, FR-087

---

### UC-005: View & Track Warranty Status

- **Actor:** Customer; Warranty Registration Officer; Claim Processing Officer
- **Precondition:** The user is logged in; at least one warranty record exists in the system
- **Main Flow:**
  1. The customer navigates to the warranty list screen; the system displays a summary of total warranties by status (Active, Expired, Pending, Rejected)
  2. Each warranty in the list is shown with a colour-coded status badge, product name, and expiry date
  3. The customer can search warranties by product name or model number and filter by status
  4. The customer selects a warranty to view its full detail screen
  5. The system displays all warranty details: product information, registration status, coverage conditions (covered parts, labour/service fee inclusion, exclusions), purchase date, expiry date, and claim history
  6. The warranty status is shown in real time, updating automatically if any change occurs without requiring a page refresh
  7. For internal staff, the system allows searching any customer's warranty by customer name, email, or product serial number
- **Alternative Flow:** If the customer's warranty list is empty, the system displays an empty state screen with a prompt to register a new warranty; if a staff member's search returns no results, a clear message is displayed and a suggestion to refine the search criteria is shown
- **Exception Flow:** If the real-time status update service is unavailable, the system displays the last known status with a visible indicator that the data may not be current; if the customer's session expires while viewing the list, the system redirects to the login screen and restores the previous screen after re-authentication
- **Postcondition:** The customer or staff member has an accurate, up-to-date view of the relevant warranty record(s); no data is modified during this interaction
- **Related Requirements:** FR-011, FR-012, FR-013, FR-015, FR-042, FR-044, FR-051, FR-082

---

### UC-006: Download Warranty Certificate

- **Actor:** Customer
- **Precondition:** The customer is logged in; the selected warranty is in Active status
- **Main Flow:**
  1. The customer navigates to the detail screen of an Active warranty
  2. The customer selects "Download Warranty Certificate"
  3. The system generates a PDF warranty certificate containing the product name, serial number, purchase date, expiry date, coverage summary, and a unique warranty reference number
  4. The PDF is generated within 5 seconds and downloaded to the customer's device
  5. The customer can open, save, or share the certificate from their device
- **Alternative Flow:** If the warranty is in Pending, Rejected, or Expired status, the download option is not displayed; the customer is shown the current status and informed that a certificate is only available for Active warranties
- **Exception Flow:** If PDF generation fails due to a system error, the system displays an error message and prompts the customer to try again; if the failure persists, the system advises the customer to contact support
- **Postcondition:** The customer has a downloaded PDF warranty certificate on their device; no warranty record data is modified
- **Related Requirements:** FR-044, FR-045

---

### UC-007: Submit Warranty Claim

- **Actor:** Customer
- **Precondition:** The customer is logged in; the selected warranty is in Active status with no existing open claim; the claim is submitted within the valid claim window (within 30 days of expiry date at most)
- **Main Flow:**
  1. The customer navigates to an Active warranty and selects "Submit Claim"
  2. The system confirms the warranty is eligible for a claim (Active status, within claim window, no open claim) and displays the coverage conditions for reference
  3. The customer enters a description of the problem and the product's current condition
  4. The customer selects their preferred resolution type from the available options
  5. The customer uploads up to 5 supporting images (max 10 MB each) to document the issue
  6. For customers with poor or no connectivity, the system allows the claim to be saved as a draft on-device for submission when connectivity is restored
  7. The customer reviews the claim summary and confirms submission
  8. The system creates the claim record with Submitted status and displays a confirmation screen showing the claim reference number, submission date, and expected response timeframe
  9. The system automatically assigns the claim to an available Claim Processing Officer using round-robin allocation
- **Alternative Flow:** If the customer decides to cancel the claim before it moves to Under Review, they can select "Cancel Claim" from the claim detail screen; the system prompts for confirmation and updates the claim status to Cancelled
- **Exception Flow:** If the warranty is Expired, Pending, Rejected, or already has an open claim, the system prevents submission and displays a clear explanation of why the claim cannot be submitted; if the submission fails due to a network error, the system saves the draft automatically and informs the customer that they can resume when connectivity is restored; if the claim exceeds 30 days past the warranty expiry date, the system blocks the submission and explains the deadline
- **Postcondition:** A claim record exists in Submitted status; the claim is assigned to a Claim Processing Officer; a confirmation notification is sent to the customer; the warranty record reflects the open claim
- **Related Requirements:** FR-016, FR-017, FR-058, FR-059, FR-066, FR-072

---

### UC-008: Process & Resolve Warranty Claim

- **Actor:** Claim Processing Officer; Claim Supervisor
- **Precondition:** One or more claims exist in Submitted or Under Review status; the officer is logged in with the correct role
- **Main Flow:**
  1. The Claim Processing Officer navigates to the claim queue; the system displays assigned claims ordered by submission date, with High Priority claims shown at the top
  2. The officer searches or filters claims by customer name, serial number, claim ID, status, date range, or assigned officer
  3. The officer selects a claim and reviews all details: customer information, warranty record, problem description, supporting images, and internal notes
  4. The officer updates the claim status to Under Review and adds an internal note summarising the initial assessment
  5. The officer and customer can exchange messages via the in-app messaging thread attached to the claim; all messages are logged
  6. If the estimated resolution value exceeds the configured cost threshold (default $500), the system automatically escalates the claim to the Claim Supervisor for secondary approval
  7. The Claim Supervisor reviews the escalated claim, adds a supervisory note, and approves or rejects it
  8. The Claim Supervisor can flag a claim as High Priority at any point, moving it to the top of the processing queue
  9. The officer approves or rejects the claim with a mandatory reason; the system updates the claim status accordingly
  10. The system sends a notification to the customer within 5 minutes of the status change
  11. Once the resolution has been fulfilled, the officer marks the claim as Completed, recording the resolution date and summary
  12. The system prompts the customer to rate their claim experience (1–5 stars) after the Completed status is set
- **Alternative Flow:** If the officer needs to export the claim list for offline review, they apply filters and select "Export to CSV"; the export event is recorded in the audit log
- **Exception Flow:** If the assigned officer does not action a claim within 24 hours, the system automatically escalates the claim to the Claim Supervisor and sends an alert; if two officers attempt to update the same claim simultaneously, the system uses optimistic locking and notifies the second officer of the conflict
- **Postcondition:** The claim is in Approved/Rejected/Completed status; the customer has been notified; all status changes, internal notes, and messages are permanently logged; the claim satisfaction rating is recorded if provided
- **Related Requirements:** FR-018, FR-019, FR-020, FR-021, FR-023, FR-025, FR-043, FR-049, FR-062, FR-063, FR-064, FR-068

---

### UC-009: Appeal Rejected Claim

- **Actor:** Customer
- **Precondition:** The customer is logged in; the claim is in Rejected status; no previous appeal has been submitted for this claim; the appeal is submitted within 14 days of the rejection date
- **Main Flow:**
  1. The customer navigates to a Rejected claim and views the rejection reason
  2. The customer selects "Submit Appeal"
  3. The system confirms the appeal is within the 14-day window and that no previous appeal exists for this claim
  4. The customer provides additional information or uploads supplementary documentation to support the appeal
  5. The customer submits the appeal
  6. The system creates the appeal record and routes it to the Claim Supervisor for review
  7. The Claim Supervisor reviews the original claim, rejection reason, and the appeal submission
  8. The Claim Supervisor approves or rejects the appeal with a mandatory reason
  9. The system notifies the customer of the appeal outcome via in-app notification and email
- **Alternative Flow:** If the customer attempts to appeal after the 14-day window, the system displays a message explaining that the appeal period has expired and the option is no longer available
- **Exception Flow:** If the appeal submission fails due to a network error, the system saves the draft and allows the customer to resubmit; if the Claim Supervisor account is inactive at the time of escalation, the system alerts the System Administrator to assign the appeal to an available supervisor
- **Postcondition:** The appeal record is created and linked to the original claim; the Claim Supervisor has reviewed and decided on the appeal; the customer has been notified of the final outcome
- **Related Requirements:** FR-022, FR-025

---

### UC-010: Receive Warranty Expiry & Claim Notifications

- **Actor:** Customer
- **Precondition:** The customer is logged in and has at least one Active warranty or open claim; notification preferences are enabled
- **Main Flow:**
  1. The system automatically checks warranty expiry dates daily via a scheduled job
  2. At 90 days, 30 days, and 7 days before a warranty's expiry date, the system sends an in-app notification and email to the customer reminding them of the upcoming expiry
  3. The notification includes the product name, expiry date, and a direct link to the warranty detail screen
  4. Whenever a claim status changes, the system sends an in-app notification and email to the customer within 5 minutes of the change, including the new status and relevant details
  5. The customer opens the in-app notification and is taken directly to the relevant warranty or claim screen
  6. On mobile devices where push notification permissions have been granted, the system also sends a push notification for expiry reminders and claim updates
- **Alternative Flow:** If the customer has opted out of email notifications for a specific notification type, the system sends only the in-app notification; push notifications are only sent if the customer has granted the app notification permissions on their device
- **Exception Flow:** If the notification delivery fails, the system retries up to 3 times using exponential backoff within 1 hour; if all retries fail, the system marks the notification as failed and alerts the System Administrator; if the scheduled notification job fails to run, an alert is sent to the System Administrator within 5 minutes
- **Postcondition:** The customer has received timely expiry reminders and claim status updates through their preferred notification channels; notification delivery events are logged
- **Related Requirements:** FR-024, FR-025, FR-088, FR-033

---

### UC-011: Manage Notification Preferences

- **Actor:** Customer
- **Precondition:** The customer is logged in
- **Main Flow:**
  1. The customer navigates to their account settings and selects "Notification Preferences"
  2. The system displays a list of all notification types: registration updates, claim updates, and expiry reminders
  3. For each notification type, the customer can independently toggle email notifications on or off
  4. The customer saves their preferences
  5. The system applies the updated preferences immediately and confirms the change
- **Alternative Flow:** If the customer turns off all notification types, the system displays an advisory message informing them they may miss important updates regarding their warranties and claims, but allows the action
- **Exception Flow:** If the preference save fails due to a system error, the customer is notified and the previous preferences are retained unchanged
- **Postcondition:** The customer's notification preferences are updated and applied to all future notification events immediately
- **Related Requirements:** FR-026

---

### UC-012: Manage Customer Profile

- **Actor:** Customer
- **Precondition:** The customer is logged in
- **Main Flow:**
  1. The customer navigates to their profile settings
  2. The customer updates one or more of the following: full name, phone number, profile photo, or saved delivery addresses
  3. The customer saves the changes; the system validates the inputs and updates the profile record
  4. If the customer wishes to change their email address, they enter the new address; the system sends a confirmation link to the new email address and the change is only applied after the customer clicks the confirmation link
  5. If the customer wishes to change their password, they enter the current password and provide a new password meeting complexity requirements; the system updates the password and logs the change in the security audit log
  6. The system confirms the profile update with a success message
- **Alternative Flow:** If the customer wishes to delete their account, they select "Delete Account" and confirm via a mandatory confirmation dialog; the system flags the account for deletion while retaining all warranty and claim records for a minimum of 7 years for audit and compliance purposes
- **Exception Flow:** If the new email address is already associated with another account, the system rejects the change and informs the customer; if the current password entered for a password change is incorrect, the system rejects the request and prompts the customer to try again
- **Postcondition:** The customer's profile is updated with the new information; email changes are only applied upon confirmation; password changes are logged; account deletion requests are queued with data retention rules applied
- **Related Requirements:** FR-027, FR-028, FR-029, FR-077

---

### UC-013: Manage Product Catalogue

- **Actor:** Product & Catalogue Manager
- **Precondition:** The manager is logged in with the correct role
- **Main Flow:**
  1. The manager navigates to the product catalogue management screen
  2. The manager can create a new product by entering the product name, model number, product category, warranty period, coverage conditions (covered parts, component-level warranty durations, labour/service fee inclusion), and linking to an authorised retailer list
  3. The manager sets configurable warranty durations per product model and per component where applicable
  4. The manager can edit any existing product record to update its details or coverage terms
  5. The manager can deactivate a product model so it no longer appears in the customer registration form; existing registered warranties for the product are not affected
  6. The manager can create, edit, and deactivate product categories used for grouping products in reports and filters
  7. The manager can maintain the authorised retailer list used to validate warranty registrations
  8. For bulk imports, the manager downloads the CSV template, populates it with product data, and uploads the file; the system validates each row and displays a pre-import error report; the manager confirms and the system commits only valid rows
  9. The manager can publish a product recall notice for a specific model; the notice is automatically displayed to all customers who have that model registered
- **Alternative Flow:** If the manager attempts to deactivate a product model that has pending warranty registrations, the system warns the manager and lists the affected registrations before allowing the action to proceed
- **Exception Flow:** If the bulk import CSV contains formatting errors or missing mandatory fields, the system rejects the import and provides a row-by-row error report without committing any records; the manager corrects the file and re-uploads
- **Postcondition:** The product catalogue is updated; new products are available for warranty registration; deactivated products no longer appear to customers; recall notices are visible to affected customers; all changes are recorded in the audit log
- **Related Requirements:** FR-030, FR-031, FR-032, FR-056, FR-057, FR-065, FR-067, FR-080

---

### UC-014: Manage Internal Users & Roles

- **Actor:** System Administrator
- **Precondition:** The System Administrator is logged in with full administrative access
- **Main Flow:**
  1. The System Administrator navigates to the user management screen
  2. The administrator creates a new internal staff account by entering the staff member's name, email address, and assigning the appropriate role (Warranty Registration Officer, Claim Processing Officer, Claim Supervisor, Product & Catalogue Manager, Reporting & Analytics Manager, Compliance & Audit Officer, or IT Support)
  3. The system sends a welcome email to the new staff member with a link to set their password and configure two-factor authentication
  4. The administrator can edit an existing staff account to update their name, email, or assigned role
  5. The administrator can deactivate a staff account; the system marks it inactive and revokes all active sessions immediately
  6. Role changes take effect immediately; the user's accessible screens and data are updated upon their next action
  7. All create, update, and deactivate actions are recorded in the audit log with the administrator's ID and timestamp
  8. Deactivated records are soft-deleted and remain retrievable in the audit log
- **Alternative Flow:** If an administrator attempts to deactivate an account that has open claims or registrations currently assigned to it, the system warns the administrator and requires re-assignment of those items before deactivating the account
- **Exception Flow:** If the welcome email fails to deliver, the system retries delivery and alerts the System Administrator if all retries fail, allowing the administrator to manually resend the invitation
- **Postcondition:** The staff account is created, updated, or deactivated as requested; role-based access permissions are applied immediately; all changes are audit-logged
- **Related Requirements:** FR-033, FR-034, FR-057, FR-071

---

### UC-015: Monitor Admin Dashboard

- **Actor:** Management Sponsor; System Administrator; Reporting & Analytics Manager
- **Precondition:** The user is logged in with a role that has dashboard access
- **Main Flow:**
  1. The user navigates to the admin dashboard
  2. The dashboard loads within 3 seconds and displays real-time KPI widgets including: total active warranties, total open claims, claims pending approval, claims under review, average claim processing time, and warranties expiring within 30 days
  3. The dashboard displays monthly trend data for: claims approved, rejected, and pending; average resolution time; and officer workload distribution
  4. The dashboard displays the warranty utilisation ratio (warranties with claims vs total active warranties) segmented by product category
  5. The user can click on any KPI widget to navigate to the relevant filtered list screen for deeper investigation
  6. Dashboard data refreshes automatically every 60 seconds without requiring a page reload
- **Alternative Flow:** If the user wishes to view data for a specific time period, they can adjust the date range filter on the dashboard to see historical trends for that period
- **Exception Flow:** If the real-time data feed is temporarily unavailable, the dashboard displays the last successfully loaded data with a visible timestamp indicating when the data was last refreshed; an alert is sent to the System Administrator
- **Postcondition:** The user has an up-to-date view of system operational metrics; no data is modified during this interaction
- **Related Requirements:** FR-035, FR-036, FR-064, FR-073

---

### UC-016: Generate & Schedule Reports

- **Actor:** Reporting & Analytics Manager; Claim Supervisor
- **Precondition:** The user is logged in with reporting access; data exists within the requested date range
- **Main Flow:**
  1. The user navigates to the reports screen
  2. For a standard report, the user selects from predefined report types: warranty registrations by period, claims by status, claims by product category, expiry reports, or staff performance reports
  3. The user sets the desired date range and any applicable filters
  4. The system generates the report and displays results within 10 seconds for standard reports
  5. The user exports the report to CSV or PDF format
  6. For a custom report, the user selects the custom report builder and chooses dimensions (date range, product category, region, claim type, officer); the system generates the report within 30 seconds for date ranges up to 12 months
  7. To schedule a recurring report, the user selects "Schedule Report", sets the frequency (daily, weekly, monthly), and specifies one or more email recipients
  8. The system saves the schedule and automatically generates and emails the report at the configured interval
  9. Claim Processing Officers can apply filters to the claim list and export the filtered results to CSV; the export event is recorded in the audit log
  10. The Reporting & Analytics Manager can view staff performance reports showing each officer's total claims handled, average resolution time, approval rate, and customer satisfaction score
- **Alternative Flow:** If no data exists for the selected report parameters, the system displays a clear message indicating that no records were found for the specified criteria and suggests adjusting the filters
- **Exception Flow:** If a report generation exceeds the 30-second limit (e.g. very large custom report), the system runs the report in the background, notifies the user via email when it is ready, and provides a download link; if a scheduled report fails to generate, the system retries once and alerts the System Administrator if the retry also fails
- **Postcondition:** The report is generated and available for download or has been emailed to the configured recipients; scheduled reports are saved and will execute automatically; all export events are logged in the audit log
- **Related Requirements:** FR-037a, FR-037b, FR-038, FR-054, FR-075, FR-081, FR-083

---

### UC-017: Review & Export Audit Logs

- **Actor:** Compliance & Audit Officer; System Administrator
- **Precondition:** The user is logged in with compliance or administrative access
- **Main Flow:**
  1. The user navigates to the audit log screen
  2. The system displays a searchable, paginated list of all audit log entries
  3. The user filters the log by date range, user ID, event type (create, update, delete, status change, login, export), or affected record type
  4. The system returns matching entries, each showing: timestamp, user ID, action type, affected record, before and after values, and IP address for security events
  5. The user reviews the log entries for compliance or investigation purposes
  6. The user selects "Export" to download the filtered results in CSV format
  7. For data privacy compliance, the user can export all personal data records for a specific customer within the required 30-day response window
  8. Warranties and claim records more than 7 years past expiry are automatically archived; archived records remain accessible to Compliance Officers from the audit screen but are hidden from standard customer views
- **Alternative Flow:** If the user needs to investigate a specific user's actions, they can filter the log by user ID to see a chronological record of all actions performed by that account
- **Exception Flow:** If the audit log export fails, the system notifies the user and retains the export request so it can be retried; if the archive job fails to run on schedule, the System Administrator is alerted within 5 minutes
- **Postcondition:** The Compliance Officer has reviewed and/or exported the required audit records; all export events are themselves logged in the audit trail; archived records remain accessible for compliance purposes
- **Related Requirements:** FR-039, FR-040, FR-046, FR-057, FR-070, FR-075, FR-081

---

### UC-018: Monitor System Health & Alerts

- **Actor:** System Administrator; IT Support Officer
- **Precondition:** The System Administrator is logged in; the system monitoring and APM tools are integrated and operational
- **Main Flow:**
  1. The System Administrator accesses the system monitoring screen, which displays real-time metrics: uptime percentage, error rate, API response times, resource utilisation (CPU, memory, storage), and active user count
  2. The system continuously monitors all critical services and scheduled jobs against defined thresholds
  3. If system uptime falls below 99%, the error rate exceeds 1% of requests, or storage utilisation exceeds 80%, the system automatically sends an alert to the System Administrator
  4. If a scheduled job (expiry notifications, report generation, data archiving) fails to complete, the system sends an alert within 5 minutes of the failure
  5. The administrator reviews the alert, investigates the issue using the centralised log management system, and takes corrective action
  6. Application errors and exceptions are logged with severity level, timestamp, stack trace, and request context; logs are retained for a minimum of 90 days
  7. The administrator can review real-time APM data to identify performance bottlenecks across all system components
- **Alternative Flow:** If the alert is a false positive, the administrator can acknowledge and dismiss it with a note; dismissed alerts remain in the alert history for audit purposes
- **Exception Flow:** If the monitoring service itself becomes unavailable, the APM tool sends an independent alert to the System Administrator via a secondary notification channel (e.g. direct email to a backup address)
- **Postcondition:** System health issues are detected, alerted, and logged promptly; the administrator has the information needed to investigate and resolve the issue
- **Related Requirements:** FR-048, FR-033, FR-043, FR-047

---

### UC-019: Configure System Settings

- **Actor:** System Administrator
- **Precondition:** The System Administrator is logged in with full administrative access
- **Main Flow:**
  1. The System Administrator navigates to the system configuration screen
  2. The administrator can edit notification templates (email and in-app) for each event type without requiring a code deployment
  3. The administrator can adjust the timing of expiry reminder notifications (e.g. change from 90/30/7 days to custom intervals)
  4. The administrator can update the claim escalation cost threshold that triggers Claim Supervisor approval; the change is logged in the audit trail
  5. The administrator can configure the SMTP or transactional email provider settings (e.g. SendGrid, AWS SES API credentials)
  6. The administrator can manage feature flags, enabling or disabling specific system features per environment without a deployment
  7. All configuration changes are saved as environment variables or via the configuration service without code changes
  8. Each configuration change is recorded in the audit log with the administrator's ID, the parameter changed, and before/after values
- **Alternative Flow:** If the administrator sets an invalid value for a configuration parameter (e.g. a non-numeric value for the escalation threshold), the system rejects the change, displays a validation error, and retains the previous valid value
- **Exception Flow:** If a configuration save fails due to a system error, the previous configuration remains active and the administrator is notified to retry
- **Postcondition:** System configuration is updated and applied immediately without a code deployment or system restart; all changes are audit-logged
- **Related Requirements:** FR-055, FR-074, FR-078, FR-084, FR-044, FR-046

---

### UC-020: Transfer Warranty Ownership

- **Actor:** Customer; Warranty Registration Officer
- **Precondition:** The customer is logged in; the warranty to be transferred is in Active status; the recipient is a registered customer in the system
- **Main Flow:**
  1. The customer navigates to the Active warranty they wish to transfer and selects "Transfer Warranty"
  2. The customer enters the email address of the recipient customer
  3. The system verifies that the recipient email matches a registered customer account
  4. The customer confirms the transfer request; the system creates a pending transfer record and routes it to a Warranty Registration Officer for approval
  5. The Warranty Registration Officer reviews the transfer request and approves it
  6. The system updates the warranty ownership to the recipient customer, records the transfer in the warranty history, and notifies both the original owner and the new owner
- **Alternative Flow:** If the Warranty Registration Officer rejects the transfer (e.g. suspicious activity), the officer provides a mandatory rejection reason and both customers are notified that the transfer was not approved
- **Exception Flow:** If the recipient's email does not match any registered account, the system informs the transferring customer that the recipient must first create an account before a transfer can proceed
- **Postcondition:** The warranty record is updated to reflect the new owner; the transfer event is recorded in the warranty history audit trail; both parties are notified
- **Related Requirements:** FR-060

---

### UC-021: Verify Warranty (Public)

- **Actor:** Any member of the public (unauthenticated)
- **Precondition:** The public-facing warranty verification page is accessible; the warranty record exists in the system
- **Main Flow:**
  1. A visitor navigates to the public warranty verification page without logging in
  2. The visitor enters the product serial number into the verification form
  3. The system searches for a warranty record matching the serial number
  4. The system displays the warranty validity status and expiry date without revealing any personal customer information
- **Alternative Flow:** If the serial number is found but the warranty is Expired or Rejected, the system displays the status and expiry date to inform the visitor that the warranty is no longer valid
- **Exception Flow:** If no warranty record is found for the entered serial number, the system displays a message stating that no registered warranty was found and suggests the product owner register the warranty if they have not done so
- **Postcondition:** The visitor has received the warranty status for the specified serial number; no customer personal data is disclosed; no system records are modified
- **Related Requirements:** FR-061

---

### UC-022: Access Help & FAQ

- **Actor:** Customer; Any unauthenticated visitor
- **Precondition:** The application is accessible; the Help & FAQ content has been published
- **Main Flow:**
  1. The user navigates to the Help & FAQ section from the application menu or login screen, accessible without requiring login
  2. The system displays categorised help articles covering: warranty registration, claim submission, account management, and common troubleshooting topics
  3. The user selects a topic and reads the help content
  4. The user can switch the display language between English and Khmer at any point
  5. All UI labels, help text, notifications, and messages are displayed in the user's selected language
- **Alternative Flow:** If the user cannot find the information they need in the FAQ, they are presented with a contact option to reach the support team
- **Exception Flow:** If the help content fails to load due to a connectivity issue, the system displays a friendly error message and suggests the user try again or contact support directly
- **Postcondition:** The user has accessed the help content they needed; no system data is modified; the selected language preference is retained for the session
- **Related Requirements:** FR-041, FR-089

---

**Summary of Consolidation:**

| Use Cases Produced | FRs Covered | Average FRs per Use Case |
|---|---|---|
| 22 Use Cases | All 90 FRs | ~4 FRs per Use Case |

All 90 functional requirements are covered across the 22 use cases, grouped by natural end-to-end user interactions. No use case maps to a single FR in isolation — each represents a complete, meaningful workflow.

Awaiting your next instruction. 🎯
