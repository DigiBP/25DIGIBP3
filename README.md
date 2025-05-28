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

The goal of our project is to digitalize the onboarding process for IT maintenance account at the Universit of Basel. We will reach this goal by implementing decision logic using DMN tables, integrating services for approval handling and email notifications. The goal is also to improve the user experience by implementing a structured workflow and a digital form. Moreover the aim is to save time to the IT Service desk and handle better errors through automated activities.

# 1. AS-IS Process

The process follows these steps:

1. The applicant (typically an external partner) submits a request form via email or paper.
2. The IT Service Desk (SD) manually verifies the completeness of the form.
3. If questions arises or details are missing, SD contacts the applicant, which can lead to multiple iterations.
4.  Once complete, the request is forwarded to the appropriate approver.
5.  The approver manually evaluates the request and either approves or rejects it.
6.  If approved, the SD checks again if the request is complete and if the applicant has questions.
7.  Once approved by the SD, the IT Service Desk manually enters the data into the SAP IDM system.
8.  SAP IDM creates account according to input
9.  The Service Desk receives the account details and manually generates a master data sheet for the applicant and send it via physical mail.
10. Once the applicant has set the password, the maintenance account is active 

![AS-IS Process](AS-IS%20Process.png)

## 1.1 Challenges of the AS-IS Process

The current onboarding process for IT maintenance accounts at the University of Basel is largely slow, paper-based and fragmented: 

Main pain points: 
1. Inconsistent process execution: Every team executes the process differently.That is in part due to the different systems and their specifications that the different teams use. No structured approval routing, causing inconsistent decision-making. 
2. Lack of transparency and standardization with most steps relying on ad-hoc execution. No clear feedback if it the request has been approved or rejected. 
3. Manual effort is high as the form is paper-based. Lots of copying, pasting, and sending repetitive emails.
4. Frequent back-and-forth communication between the IT Service Desk and the requestors leading to inefficiencies and delays.
5. Lots of errors: service desk (and approver) did not correctly filled out the paper form or additional permissions due to paper-based 
6. Security risks due to uncontrolled account expiration and access permissions. The applicant usually needs this account only once or twice per year. The existing account needs to be extended (or reactivated) once a year. The deletion of such an account is not yet controlled, meaning that after one year, it expires if not extended, but at the same time, it is not actively deleted (for example, if the service provider is changed)
7. The lack of automation in determining suitable accounts results in a manual and error-prone decision-making process.

The basic account types do not accommodate those diverse permission requirements, creating frequent mismatches with operational needs. This leads to the situation where permission requests cannot be fully handled through the form, necessitating additional steps via ticketing and the SD.

This challenges highlights the need for digital transformation, which our To-Be process aims to address.

# 2. TO-BE: 

## 2.1 Overview of the TO BE Process 

![TO-BE Process Final](https://github.com/user-attachments/assets/2fbe2321-d153-4dcc-87ed-8d82503d2f47)


- The process begins with the submission of a google form by the applicant.
- The form is received and forwarded to the appropriate approver thanks to a DMN Table.
- The approver checks the form for completeness and requests feedback via email if necessary.
- A rejection email is sent if the request is denied.
- If approved, the process continues with forwarding the form to the Service Desk. The Service Desk checks the technical requirements and also has the possibility to request feedback if necessary.
- If the Service Desk finds no problem, the account creation and password generation are initiated.
- Account information and credentials are dispatched via post and the customer is informed via email.
- The process outcomes are stored in a google sheet. 

Swimlanes: 
- New: "Backend System" --> explanation?
- Removed: "SAP IDM" from swimlanes and added it in a new pool --> explanation?


## 2.2 How to run the process 

  ## 2.2.1 Service tasks :gear:
 
### :e-mail: *Implementation of four automated emails in Make: 

1. Send Rejection Email: Sent when a request is denied.
2. Send Email asking for feedback: Sent when additional information is required from the applicant.
3. Receive Email Feedback: The applicant sends their feedback to a designated email adress.
4. Send Information Email: Sent after account creation to inform the applicant that credentials will be sent via post.

Each email scenario is triggered via a "custom webhook" module and executed through "My others SMTP connections" using the module "send an email" except for the "Receive Email Feedback" which uses a Custom Mailhook and then sends the data to the process via HTTP PUSH.

**GMX – Email Handling (Applicant Notification & Replies)**

GMX serves as the communication hub for interacting with applicants throughout the process. It is used to send and receive structured email messages, including feedback, and  approvals. These email communications is automatically triggered by Make and Camunda, ensuring timely and consistent updates to the applicant.

![Send Rejection Email_Make](https://github.com/user-attachments/assets/b4b420ec-b088-450b-9dd3-88a33d155ffa)

![Receive Email Feedback 1](https://github.com/user-attachments/assets/81703a19-3456-47a0-839c-cdd2704b1324)





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

1. Process Standardization
- Ensures consistent handling of all onboarding requests.

2. Automation of Repetitive Tasks
- Automated email notifications (rejection, feedback request, approval).
- Automatic routing to correct approver via decision tables.

3. Faster Turnaround Time
- Requests are processed and routed immediately without waiting on manual forwarding.
- Feedback loops and approvals are streamlined, reducing unnecessary back-and-forth.
  
4. Improved User Experience
- Applicants complete their request in a google form and not on paper anymore. 
- Applicants receive immediate confirmation and updates via email.

5. Error Reduction
- Decision tables reduce human error in determining permissions or routing logic.

6. Better Traceability and Transparency
- More steps are logged and traceable (e.g.
- Better traceability with the storage of the data form in the google sheet. 

7. Integration with External Tools
- Connected with Google Forms (front-end) and Make (workflow automation).
- Simulated integration with SAP IDM, ready for future real system connections.
  
8. Time Savings for IT Staff
- Fewer manual approvals and emails.


# Technologies 


- BPMN 2.0                 
- DMN                   
- Camunda 7                 
- Camunda REST API                   
- Make.com                   
- HTTP Requests                   
- Google Forms                 
- Google Sheets                  
- GMX Email Adress 


# Conclusion 

The digitalization of the Onboarding Process for external partners at the University of Basel represents an advancement over the previous manual workflow. By integrating tools like Google Forms, Make, and Camunda, the project automates key steps such as form submission, approval routing, email notifications, and data storage. The new process minimizes manual errors, shortens turnaround times, and improves transparency for all stakeholders. With the use of DMN tables, approvals and permission assignments are now managed more consistently and accurately, while automated emails keep applicants informed throughout the process. Although some human tasks remain, the overall workload for the IT Service Desk and approvers has been reduced. The project not only modernizes a fragmented process but also establishes a solid foundation for future integrations, providing a scalable and user-friendly framework for managing maintenance account requests.








