# Team Members

| Name                    | Email          |
|-------------------------|----------------|
| Jonas Ferro             |                |
| Malanie Romero Orreaga  |                |
| Jan Locher              |                |
| Justine Herzog          | 		   |

# Coaches

- Andreas Martin  
- Charuta Pande  

# Project Goals

Digitalize and automate the IT maintenance account onboarding process by implementing decision logic using DMN tables, integrating external services for approval handling and email notifications. The goal is to improve the user experience through structured workflows and form submissions and also to save time the approver through automated activities.

# AS-IS Process

The current onboarding process for IT maintenance accounts at the University of Basel is largely manual and fragmented. It involves multiple steps, approvals, and frequent back-and-forth communication, leading to inefficiencies and delays.

The process follows these steps:
	1. The applicant (typically an external partner) submits a request form via email or paper.
	2. The IT Service Desk (SD) manually verifies the completeness of the form.
	3. If questions arises or details are missing, SD contacts the applicant, which can lead to multiple iterations.
	4. Once complete, the request is forwarded to the appropriate approver.
	5. The approver manually evaluates the request and either approves or rejects it.
	6. If approved, the SD checks again if the request is complete and if the applicant has questions. 
	7. Once approved by the SD, the IT Service Desk manually enters the data into the SAP IDM system.
	8. SAP IDM creates account according to input 
	9. The Service Desk receives the account details and manually generates a master data sheet for the applicant and send it via physical mail. 
  	10. Once the applicant has set the password, the maintenance account is active 

![AS-IS Process](AS-IS%20Process.png)

## Challenges of the AS-IS Process

- The basic account types do not accommodate those diverse permission requirements, creating frequent mismatches with operational needs. This leads to the situation where permission requests cannot be fully handled through the form, necessitating additional steps via ticketing and the SD.
	- The lack of automation in determining suitable accounts results in a manual and error-prone decision-making process.
	- Frequent manual errors due to paper-based handling.
	- No structured approval routing, causing inconsistent decision-making.The excessive signature requirements for the maintenance account cause confusion and inefficient communication with service providers (applicants). Also, sometimes the form is sent to the wrong approver.
	- Security risks due to uncontrolled account expiration and access permissions. The applicant usually needs this account only once or twice per year. The existing account needs to be extended (or reactivated) once a year. The deletion of such an account is not yet controlled, meaning that after one year, it expires if not extended, but at the same time, it is not actively deleted (for example, if the service provider is changed).
	- Every team handles the process and the permissions a bit differently. That is in part due to the different systems and their specifications that the different teams use. A system for managing a server differs from a system for managing networking and so on. Some internal interviews with employees of six different teams revealed the different use cases and requirements of those teams (see Appendix F: Notes from Internal Interviews (Intranet)).  (not sure if we keep that) 

This challenges highlights the need for digital transformation, which our To-Be process aims to address.

# How to Run the Process

1. **Set Up the Google Form**  
   - Ensure the form is designed with all required fields.  
   - Configure responses to be saved in Google Sheets.

2. **Integrate Google Form with Make (Integromat)**  
   - Use Make to monitor new submissions.
   - Set up an automation scenario that transfers form responses to Camunda.

3. **Trigger the Camunda Workflow**
   - Once Make detects a new form submission, it triggers the process instance in Camunda.
   - The request data (applicant details, access type, required approvals) is passed as process variables.

4. **Decision Table**
   - Camunda evaluates the request based on predefined DMN decision tables.
   - Depending on the decision outcome, the request is either forwarded for approval or flagged for revision.

5. **Approval Workflow**
   - If approval is required, the process routes the request to the correct approver based on role-based rules.
   - The approver receives a task in Camunda Tasklist and can approve or reject the request.

6. **Automated Email Notification**
   - Once the request is approved, an automated email is sent to the applicant with login details and instructions.
   - If rejected, the applicant is notified via email with the reason for rejection. 

7. **Automated Email Notification**
   - In this prototype, account creation is mocked using a Flask API or a simulated service.
   - Future integration could include real SAP IDM system integration for actual account provisioning.


# TO-BE: 
## 6.1 Make Scenarios
  ### 6.1.1Google Form 
  ### 6.1.2 Send Email notifications
  ### 6.1.3 Simulation of SAP Account creation 
## 6.2 Integration of DMN - Decision Tables

# Benefeits 

# Technologies 

# Conclusion 

# References 



