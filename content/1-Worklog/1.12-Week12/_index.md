---
title: "Week 12 Worklog"
date: 2026-06-29
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

 
{{% notice note %}}
**Info:** Final project report for Week 12 - validating, deploying, testing, improving, cleaning up, and finalizing the VDCMS AWS project.
{{% /notice %}}

**Period:** 29/06/2026 - 04/07/2026

## Week 12 Objectives:

* Validate and deploy the VDCMS AWS CloudFormation stack.
* Test the full system end-to-end through CloudFront and fix deployment issues.
* Finalize documentation, architecture diagram, cost review, cleanup, and presentation content.

## Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 29/06 | Checked CloudFormation with validate-template and Change Set; reviewed IAM permissions required for CloudFormation to create Roles, Instance Profiles, and pass Roles to EC2/Transcribe. | 29/06/2026 | 29/06/2026 | CloudFormation Validation |
| 30/06 | Deployed the VDCMS stack on AWS; analyzed rollback events, fixed incompatible Cognito/CloudFront properties, and completed cleanup steps for retained resources after failed stack deployment. | 30/06/2026 | 30/06/2026 | AWS Stack Deployment |
| 01/07 | Redeployed the stack successfully; verified Target Group health, RDS connection, API health check, built the Frontend, synced it to S3, and created a CloudFront invalidation for the new version. | 01/07/2026 | 01/07/2026 | CloudFront/S3/RDS/API |
| 02/07 | Performed end-to-end testing on the CloudFront domain; configured SES Identity and Sandbox, fixed WAF blocking multipart upload, corrected S3 permissions for Transcribe, and confirmed successful Vietnamese speech-to-text conversion. | 02/07/2026 | 02/07/2026 | E2E Testing |
| 03/07 | Standardized EC2 to Node.js 20; configured and tested systemd services for Backend API and SQS Transcribe Worker, then improved report, checklist, and detail modal interfaces. | 03/07/2026 | 03/07/2026 | EC2 systemd |
| 04/07 | Tested all Admin, Manager, and Engineer flows; reviewed costs, deleted the stack, S3 buckets, RDS snapshots, and SES Identity after testing, and finalized the AWS architecture diagram and project presentation. | 04/07/2026 | 04/07/2026 | Final Cleanup |

## Week 12 Achievements & Learnings:

* Successfully deployed and validated the VDCMS stack on AWS after resolving rollback issues.
* Confirmed the full user workflow and Vietnamese speech-to-text path through the AWS architecture.
* Completed cost review, resource cleanup, architecture diagram, and final presentation preparation.

