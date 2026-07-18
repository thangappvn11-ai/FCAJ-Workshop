---
title: "Post-Deployment Resource Status Validation"
date: 2026-07-08
weight: 5
chapter: false
pre: " <b> 5.5 </b> "
---

#### Objective

Verify that the core services have been correctly provisioned by CloudFormation according to the standard security architecture.

#### Services to be Actually Validated on the AWS Dashboard

| Service Name | Actual Provisioned Resource Name | Operational Status |
| --- | --- | --- |
| Amazon S3 | `vdcms-prod-frontendbucket-eltg7i7qejwf` (Frontend) & `vdcms-prod-databucket-fd8alu3qezcr` (Data) | Created |
| Amazon EC2 | `vdcms-prod-backend` (t3.small) | Running |
| Amazon RDS | `vdcms-prod-database` (Engine: MySQL Community / Aurora) | Available |
| Amazon CloudFront | Distribution `E1R0G8NW5808J0` | Enabled |
| Amazon SQS | `vdcms-prod-TranscribeQueue` & `vdcms-prod-TranscribeDeadLetterQueue` | Created |
| Amazon Cognito | User pool `vdcms-prod-users` with groups: `admin`, `manager`, `engineer` | Created |
| AWS Secrets Manager | `DatabaseServer-Secret...`, `JwtSecret-...` | Stored securely |
| AWS WAF & Shield | `vdcms-prod-regional-waf` (4 rule groups) | Applied |
| Amazon CloudWatch | RDS Log groups (TLS parameter history) | Available |

* **Amazon S3:** Confirm that the two buckets have been successfully created: `vdcms-prod-frontendbucket-eltg7i7qejwf` containing the static Frontend source code, and `vdcms-prod-databucket-fd8alu3qezcr` storing the audio files and temporary files of Amazon Transcribe.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-3.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon EC2:** Check the **Instances** section, confirm the `vdcms-prod-backend` server (instance type t3.small) is in the **Running** state.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon RDS:** Access the Aurora and RDS management console, check the **Databases** section to ensure the `vdcms-prod-database` cluster (using MySQL Community Engine) is operating stably in the **Available** state.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-5.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon CloudFront:** Confirm the distribution ID `E1R0G8NW5808J0` is turned on in **Enabled** mode to serve content routing.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-2.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon SQS:** Ensure the two queues, `vdcms-prod-TranscribeQueue` and the dead-letter queue `vdcms-prod-TranscribeDeadLetterQueue`, have been successfully initialized to handle asynchronous audio processing tasks.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-4.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon Cognito:** Check the **User pools** cluster, confirm the presence of the `vdcms-prod-users` pool. Dive into the **Groups** configuration section; the IaC system has automatically pre-created 3 core permission groups for the application: `admin`, `manager`, and `engineer`.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-8.png" style="max-width:100%; margin-bottom:16px;" />

* **AWS Secrets Manager:** Ensure the system is storing secret key strings such as Database account credentials (`DatabaseServer-Secret...`) or JWT token signing keys (`JwtSecret-...`) securely.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-7.png" style="max-width:100%; margin-bottom:16px;" />

* **AWS WAF & Shield:** Check the Regional web application ACL partition, validate that the application firewall policy `vdcms-prod-regional-waf` is directly applying the security configuration, which includes 4 processing rule groups: preventing malicious spoofing behaviors, controlling bad IPs, limiting request rate per IP, and scanning for malicious code.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-6.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon CloudWatch:** Access the **Log groups** section, dive into the RDS event log streams to check the entire initialization query history and the configuration of MySQL's TLS connection security parameters.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-1.png" style="max-width:100%; margin-bottom:16px;" />