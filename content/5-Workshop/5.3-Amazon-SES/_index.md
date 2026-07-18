---
title: "Detailed Email Authentication Configuration"
date: 2026-07-08
weight: 3
chapter: false
pre: " <b> 5.3 </b> "
---

#### Objective

Ensure the system has a reliable communication channel to automatically send account registration notifications and task approval reminders.

#### Detailed Implementation Process

1. Access the AWS Management Console in the Singapore region (`ap-southeast-1`) and search for the **Amazon Simple Email Service (SES)**.
2. On the left navigation menu, select **Identities** and click the **Create identity** button.
3. Choose the Identity type as **Email address**. In the data field, enter the address **admin.vdcms@gmail.com**, then click the **Create identity** button at the bottom. The initial status of this identity will be displayed as **Verification pending**.
4. Log into the personal Gmail inbox, open the email received from Amazon Web Services – "Email Address Verification Request", and click the 24-hour verification URL link provided by AWS to complete the authentication process.
5. Return to the SES Console interface, refresh the page, and confirm that the **Identity status** column has changed to a green checkmark indicating **Verified**.
![](/images/5-Workshop/5.3-Amazon-SES/5-3.png)