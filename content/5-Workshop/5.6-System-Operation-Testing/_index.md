---
title: "Real-world Business Flow Testing Scenario"
date: 2026-07-08
weight: 6
chapter: false
pre: " <b> 5.6 </b> "
---

#### Objective

Test the correctness of the entire software functional flow, data distribution process, and the integration capability of AWS infrastructure services through the public application interface configured by CloudFront.

#### Detailed Deployment Steps and Testing Scenarios

**1. General Interface Check and Account Configuration**

* Access the CloudFront distribution link. The system displays the introduction page with information about the Voice-Driven Construction Management System (VDCMS) solution.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6.png" style="max-width:100%; margin-bottom:16px;" />

* Check the language toggle function (VI/EN) and the display interface toggle (Dark/Light mode) to ensure static resources operate correctly.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-1.png" style="max-width:100%; margin-bottom:16px;" />
<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-2.png" style="max-width:100%; margin-bottom:16px;" />

* Log in with the previously retrieved Admin account.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-34.png" style="max-width:100%; margin-bottom:16px;" />

* Access the **Account Management** section: Test updating personal information, changing passwords, and uploading an avatar to validate the user data recording flow.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-3.png" style="max-width:100%; margin-bottom:16px;" />

---

**2. Engineer Account Provisioning via AWS Systems Manager Session Manager**

Due to strict security requirements for field engineer accounts, this provisioning flow is executed directly by accessing the Backend server instead of free registration on the UI.

* Open **AWS CloudShell** or a command-line environment with administrative privileges, and execute the command to launch a session connecting directly to the EC2 server without opening public SSH ports:

```bash
aws ssm start-session --target "$INSTANCE_ID" --region ap-southeast-1
```

* After successfully accessing the server session, execute the following Node.js script to extract environment variables, establish a secure SSL connection to the RDS database, and directly insert the encrypted authenticated engineer information:

```bash
cd /opt/vdcms/BE && sudo node -e "
const mysql = require('/opt/vdcms/BE/node_modules/mysql2/promise');
const bcrypt = require('/opt/vdcms/BE/node_modules/bcryptjs');
const fs = require('fs');

async function add() {
  const env = fs.readFileSync('/opt/vdcms/BE/.env', 'utf8');
  const get = (k) => env.match(new RegExp(k + '=(.+)'))?.[1]?.trim();

  const conn = await mysql.createConnection({
    host: get('DB_HOST'),
    user: get('DB_USER'),
    password: get('DB_PASSWORD'),
    database: 'smartconstruction',
    ssl: {
      rejectUnauthorized: true,
      ca: fs.readFileSync('/opt/vdcms/global-bundle.pem', 'utf8')
    }
  });

  const hash = await bcrypt.hash('Engineer1!', 10);

  await conn.execute(
    \`INSERT INTO users 
      (username, fullname, email, password, role, status, email_verified_at, department, job_title)
     VALUES 
      (?, ?, ?, ?, 'engineer', 'active', NOW(), 'Kỹ thuật', 'Kỹ sư công trình')\`,
    ['engineer1', 'Kỹ Sư Một', 'engineer1@vdcms.local', hash]
  );

  console.log('Successfully created engineer1 account and email has been verified');
  await conn.end();
}

add().catch(console.error);
"
```

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-4.png" style="max-width:100%; margin-bottom:16px;" />

* Enter the `exit` command to safely exit the session. The test account is established: `engineer1` / `Engineer1!`.

---

**3. Manager Registration & Approval Flow**

* On the public Web interface, select the **Register Account** section. Proceed to enter the information for the management level including: Username (`manager`), Password (`Manager1!`), Email (`thoai352004@gmail.com`).

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-5.png" style="max-width:100%; margin-bottom:16px;" />

* After submitting the request, this account will fall into the pending approval state. Log into the system as the super Admin (`vdcmsadmin`). Access the **User Management** section, the system will display the registration request of `manager` with the **Pending approval** status. The Admin clicks the **Approve** button.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-6.png" style="max-width:100%; margin-bottom:16px;" />
<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-7.png" style="max-width:100%; margin-bottom:16px;" />

* After being approved by the Admin, the user logs in with the `manager` account. The system will require email verification through an activation link/code sent directly from AWS SES to the inbox `thoai352004@gmail.com`. The user performs activation to complete the authentication process.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-8.png" style="max-width:100%; margin-bottom:16px;" />
<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-9.png" style="max-width:100%; margin-bottom:16px;" />

---

**4. Admin Business Flow (Project Management, Logs & Service Check)**

* With **Admin** privileges, access the **Projects** section and click **Create project**. Enter test information: Project name (`bababa`), Location (`HCM`), Project description (`hihi`), and choose to assign responsibility to the Manager account (`manager`), set the project execution time, and click **Save**.

* The Admin proceeds to assign some core tasks to the Manager account (`manager`) for high-level coordination.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-10.png" style="max-width:100%; margin-bottom:16px;" />

* Access the **AWS Services** section: Check the display status of the service configuration monitoring dashboard to ensure the system has correctly identified the connection settings to the AWS SDK and related Cloud resources.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-27.png" style="max-width:100%; margin-bottom:16px;" />

* Access the **Activity Log** section: Check the centralized log tracking system to ensure all actions such as project initialization, task handover, or account approval are transparently recorded by the system for operational monitoring purposes.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-35.png" style="max-width:100%; margin-bottom:16px;" />

---

**5. Manager Business & Task Coordination Flow**

* Log in with the Project Manager account (`manager`). Access the project section to check the list of assigned projects (Confirm the project `bababa` displays correctly).

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-11.png" style="max-width:100%; margin-bottom:16px;" />

* View the information of tasks assigned by the Admin and proceed to update the corresponding task progress status.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-14.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-15.png" style="max-width:100%; margin-bottom:16px;" />

* Perform the coordination function: Select project `bababa`, choose the **Assign Task** function to set up a new task for the engineer (`engineer1`). Enter task information: Task name (`farm`), detailed description, and set a deadline.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-12.png" style="max-width:100%; margin-bottom:16px;" />

* Check the engineer's report and approve it.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-22.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-23.png" style="max-width:100%; margin-bottom:16px;" />

---

**6. Field Engineer Flow & Voice Processing Integration Testing (AWS Transcribe + SQS)**

* Log in with the engineer account (`engineer1`). Access the **Work Schedule** section to visually check the deadlines of assigned tasks on the calendar board.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-28.png" style="max-width:100%; margin-bottom:16px;" />

* Access the `farm` task, check off completed task items in the acceptance **Checklist**, and update the overall progress to 100%.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-16.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-36.png" style="max-width:100%; margin-bottom:16px;" />

* **Voice Transcribe integration testing:**

  1. In the task report creation frame, click the **Microphone** icon. The browser will request and receive permission to access the hardware device.

  <img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-17.png" style="max-width:100%; margin-bottom:16px;" />

  <img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-19.png" style="max-width:100%; margin-bottom:16px;" />

  2. The engineer starts reading the field report content (Example: *"Hello, hello, hello"*).

  3. Returning to the application interface, the text snippet automatically translated by the **Amazon Transcribe** service appears fully and accurately in the report content without manual data entry.

  <img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-20.png" style="max-width:100%; margin-bottom:16px;" />

  <img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-21.png" style="max-width:100%; margin-bottom:16px;" />

* The engineer enters additional completion volume data (`20m`), uploads the actual construction site evidence image file, and executes **Submit report**. Furthermore, the engineer can use the **Export CSV Data** feature directly to export the progress report spreadsheet.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-18.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-23.png" style="max-width:100%; margin-bottom:16px;" />

---

**7. Secure Data Storage & Project Documentation Testing on Amazon S3**

* In the **Project Documents** section, upload a technical document file in PDF format to the shared data repository of project `bababa` (Data will be pushed directly to the AWS S3 Data Bucket).

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-32.png" style="max-width:100%; margin-bottom:16px;" />

* **Check storage status on AWS S3:**

  Access the **AWS S3 Console**, go to the system's Data Bucket, and navigate to the `projects/` folder path.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-33.png" style="max-width:100%; margin-bottom:16px;" />


---

**8. Incident Management and Direct Communication Channel**

* **Safety incident reporting flow:** Engineers at the construction site encountering an incident will access the **Incident Report** section, enter actual location coordinates, issue content, select the severity level, and attach on-site evidence images to send to the system.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-24.png" style="max-width:100%; margin-bottom:16px;" />

* Log back in with the **Admin** account, access the safety incident section to receive information. Proceed to handle it through the **Incident Handling Plan** form: Update the resolution status, assign the responsible person, processing deadline, identify root causes, propose corrective/preventive measures, and upload images after the incident has been resolved for tracking.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-25.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-26.png" style="max-width:100%; margin-bottom:16px;" />

* **Direct Chat feature testing:** Access the Chat module between the Engineer (`engineer1`) and Manager (`manager`). Send text messages, attachments, and voice files back and forth to test the low latency of Socket.IO. Log in to the Admin account to confirm that the Admin has centralized monitoring rights over the system chat flow and can execute commands to lock/unlock the chat channel when necessary (Chat data is managed and stored via the S3 Data Bucket).

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-29.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-30.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-31.png" style="max-width:100%; margin-bottom:16px;" />

---

**9. Centralized Monitoring Layer Validation via Amazon CloudWatch Logs**

* Access the **AWS CloudWatch Console** administration system, navigate to the **Log groups** section.

* Check and access the storage log group of the database with the prefix `/aws/rds/...`.

* Confirm that system log records, connection errors, and slow query logs from Amazon RDS are being pushed continuously and visually, proving that the security monitoring and tracking system is operating stably and accurately according to the architectural design.

<img src="/images/5-Workshop/5.6-System-Operation-Testing/5-6-37.png" style="max-width:100%; margin-bottom:16px;" />