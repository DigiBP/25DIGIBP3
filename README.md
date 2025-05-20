# Team Members

| Name                    | Email          |
|-------------------------|----------------|
| Jonas Ferro             | jonas.ferro@students.fhnw.ch               |
| Malanie Romero Orreaga  | melanieyajahira.romeroorreaga@students.fhnw.ch               |
| Jan Locher              | jan.locher@students.fhnw.ch               |
| Justine Herzog          | justine.herzog@students.fhnw.ch |

# Coaches

- Andreas Martin  
- Charuta Pande
- Devid Montecchiari

# Project Goals

Digitalize and automate the IT maintenance account onboarding process by implementing decision logic using DMN tables, integrating external services for approval handling and email notifications. The goal is to improve the user experience through structured workflows and form submissions and also to save time the approver through automated activities.

# 1. AS-IS Process

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

## 1.1 Challenges of the AS-IS Process

- The basic account types do not accommodate those diverse permission requirements, creating frequent mismatches with operational needs. This leads to the situation where permission requests cannot be fully handled through the form, necessitating additional steps via ticketing and the SD.
	- The lack of automation in determining suitable accounts results in a manual and error-prone decision-making process.
	- Frequent manual errors due to paper-based handling.
	- No structured approval routing, causing inconsistent decision-making.The excessive signature requirements for the maintenance account cause confusion and inefficient communication with service providers (applicants). Also, sometimes the form is sent to the wrong approver.
	- Security risks due to uncontrolled account expiration and access permissions. The applicant usually needs this account only once or twice per year. The existing account needs to be extended (or reactivated) once a year. The deletion of such an account is not yet controlled, meaning that after one year, it expires if not extended, but at the same time, it is not actively deleted (for example, if the service provider is changed).
	- Every team handles the process and the permissions a bit differently. That is in part due to the different systems and their specifications that the different teams use. A system for managing a server differs from a system for managing networking and so on. Some internal interviews with employees of six different teams revealed the different use cases and requirements of those teams (see Appendix F: Notes from Internal Interviews (Intranet)).  (not sure if we keep that) 

This challenges highlights the need for digital transformation, which our To-Be process aims to address.



# 2. TO-BE: 

## 2.1 Overview of the TO BE Process 

--> screenshot of the to be process 

- The process begins with the submission of a google form by the applicant.
- The form is received and forwarded to the appropriate approver.
- The approver checks the form for completeness and requests feedback if necessary.
- A rejection email is sent if the request is denied.
- If approved, the process continues with forwarding the form to the Service Desk. The Service Desk checks the thechnical requirements and also has the possibility to request feedback if necessary.
- If the Service Desk finds no problem, the account creation and password generation are initiated.
- Account information and credentials are dispatched via post and the customer is informed via email.
- The process outcomes are stored in a google sheet, and a confirmation email is sent to the applicant.

Swimlanes: 
- New: "Backend System" --> explanation? 
- Removed: "SAP IDM" from swimlanes and added it in a new pool --> Explanation? 


## 2.2 How to run the process 

  ## 2.2.1 Service tasks :gear:
 
### :e-mail: *Implementation of four automated emails in Make: 

1. Send Rejection Email: Sent when a request is denied.
2. Send Email asking for feedback: Sent when additional information is required from the applicant.
3. Receive Email Feedback: The applicant sends their feedback to a designated email adress.
4. Send Information Email: Sent after account creation to inform the applicant that credentials will be sent via post.

Each email scenario is triggered via a "custom webhook" module and executed through "My others SMTP connections" using the module "send an email" except for the "Receive Email Feedback" which uses a Custom Mailhook and then sends the data to the process via HTTP PUSH.

**GMX – Email Handling (Applicant Notification & Replies)**

GMX serves as the communication hub for interacting with applicants throughout the process. It is used to send and receive structured email messages, including feedback, and  approvals. These email communications is automatically triggered by tools like Make and Camunda, ensuring timely and consistent updates to the applicant.

![Send Rejection Email_Make](https://github.com/user-attachments/assets/b4b420ec-b088-450b-9dd3-88a33d155ffa)

![Receive Email Feedback 1](https://github.com/user-attachments/assets/81703a19-3456-47a0-839c-cdd2704b1324)

<img width="830" alt="Send Information Email" src="https://github.com/user-attachments/assets/1167d798-d1a3-49a9-918a-324d5e5ba59a" />



### * SAP IDM response:

This Make scenario fakes the SAP IDM system to which we do not have access. It responds to the process with the following message: "The {{1.system}} accounts for {{1.eSurname}} {{1.eFamilyName}} has sucsessfully been created."

![SAP IDM Response](https://github.com/user-attachments/assets/a8b37947-9abb-4efb-9bf6-55edc6a1c5f0)


### * Store Process Outcome:

This step stores all variables from the google form and data generated by the process in a Google Sheet.

![Store Process Outcome](https://github.com/user-attachments/assets/b2c553a8-8ae6-47b0-93a4-7ea66dc82270)

  
  ## 2.2.2 Decisions Tables :memo: 

1. DMN Implementation - Forward to Approver

This DMN table is used to determine the correct approver based on the faculty and department identified in the application form.

Inputs include:
- Faculty (e.g., Theology, Law...)
- Approver List (e.g., predefined list of approvers for each faculty)

Outputs the correct approver based on predefined business rules and mappings. This ensures that requests are routed to the appropriate decision-makers, minimizing routing errors.

<img width="1251" alt="Forward To Approver DMN " src="https://github.com/user-attachments/assets/e8085382-6a5e-4986-9263-be883ccfca33" />


2. DMN Implementation – Account Permissions:

This DMN table defines the permission levels granted to different external partners based on their role and internal team association. This table helps standardize permission allocation, reducing manual decision-making and ensuring consistent access rights.

<img width="1249" alt="Account Permissions DMN " src="https://github.com/user-attachments/assets/9cfa1af0-8998-4a09-b39b-fdaadd7d137c" />


## 2.2.3 User Interface :desktop_computer:

1. **The Google Form**

<img width="215" alt="image" src="https://github.com/user-attachments/assets/e5b7b512-3273-4602-8dfa-3e68ab4273fd" />
<img width="242" alt="image" src="https://github.com/user-attachments/assets/ba5e7e5b-687a-4a8a-889c-f33bdbdf4ef6" />

To improve and digitize the form submission process, Google Forms is used as a user-friendly and standardized input method. Instead of sending forms via paper or email, applicants can fill out a structured digital form online. This form includes validation rules and conditional logic to guide users through the process, ensuring all required information is provided correctly.
This approach offers several benefits for the user:
- Standardization of requests to reduce inconsistencies in submissions.
- Error reduction is achieved through the enforcement of mandatory fields and proper input formats. 
- Ease of use, since external partners can access the form from any device via a simple link.
- Instant submission allows the collected data to be fed directly into downstream systems, such as Make or Google Sheets, for further processing and automation.



2. **Sending Form Data to the Process**

The data from the filled out form is sent to the process via HTTP POST. This also starts a new process instance.

![Start Process: Receive Form Content](https://github.com/user-attachments/assets/8efccc06-8bc7-4be1-a834-232d8fea760e)

This image displays a Make automation scenario that connects Google Sheets with a system via an HTTP request, and it was a helpful step in our project to link Google Forms with Camunda. 
In the workflow, applicants submit requests through a Google Form. The responses from that form are automatically stored in a Google Sheet. To trigger the Camunda process based on this submission, we use Make to monitor the sheet and send the data to Camunda via HTTP.

**Google Sheets**

In the workflow, applicants submit requests through a Google Form. The responses from that form are automatically stored in a Google Sheet. To trigger the Camunda process based on this submission, we use Make to monitor the sheet and send the data to Camunda via HTTP.

**HTTP (Make a request)**

In this step, the HTTP module in Make is used to send a POST request to the Camunda REST API, triggering the start of a new workflow instance. The request includes a payload containing the form data extracted from the new Google Sheet row, such as the applicant’s details, type of access requested, and any additional information submitted through the form. The HTTP request is directed to a specific Camunda endpoint designed to initiate a process, allowing Camunda to automatically begin handling the request based on the predefined BPMN workflow. This seamless connection ensures that every valid form submission enters the structured review and approval process without delay.

**Camunda**

To orchestrate and manage the workflow, Camunda serves as a robust process automation engine. Camunda manages each stage of the request lifecycle, including reviews, approvals, and decision points. It tracks the status of each request in real time, whether it is awaiting an approver, returned for corrections, or ready for completion.
Key benefits of using Camunda in this process include:
- Process transparency: Camunda provides a clear overview, allowing stakeholders to track the status of every request at any stage.
- Custom workflows: It can model and execute complex processes, including conditional paths and loops for handling incomplete or incorrect submissions.
- Role-based access control: Only designated users can review and make decisions, ensuring security and compliance.
- Audit trails: Every action is recorded, providing traceability, enhanced reporting, and opportunities for process improvement.
By implementing Camunda, Basel University can gain control, visibility, and flexibility over the entire approval and account creation process.





## 2.2.4 User Tasks :bust_in_silhouette:

In our To BE process we still have remaining user tasks such as: 
- Check Form
- Read and Approve Answer
- Implement Feedback
- Approve/Reject Request
- Check Approved Request
- Read and Approve Answer (Feedback Loop)
- Check the SAP response and approve

# 3. Benefits 

# Technologies 

| Component               | Purpose           |
|-------------------------|-------------------|
| BPMN 2.0 | ???                  |
| DMN | ???                  |
| Camunda 7 | ???                  |
| Camunda REST API | ???                  |
| Make.com | ???                  |
| HTTP Requests | ???                  |
| Google Forms | ???                  |
| Google Sheets | ???                  |
| GMX Email Adress | ???                  |


# Conclusion 

# References 



