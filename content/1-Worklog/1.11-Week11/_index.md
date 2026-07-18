---
title: "Week 11 Worklog"
date: 2026-06-22
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

 
{{% notice note %}}
**Info:** Cloud deployment report for Week 11 - mapping VDCMS to AWS services and preparing infrastructure, async speech processing, identity, email, and operation components.
{{% /notice %}}

**Period:** 22/06/2026 - 28/06/2026

## Week 11 Objectives:

* Design the VDCMS AWS deployment architecture and infrastructure services.
* Create CloudFormation resources for networking, compute, database, storage, CDN, and WAF.
* Build asynchronous voice transcription, email verification, authentication, secrets, and EC2 operation support.

## Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 22/06 | Analyzed VDCMS deployment requirements and selected CloudFront, S3, WAF, ALB, EC2, RDS, SQS, Transcribe, SES, Cognito, Secrets Manager, Systems Manager, and CloudWatch for the system. | 22/06/2026 | 22/06/2026 | AWS Architecture |
| 23/06 | Built CloudFormation for a VPC across two Availability Zones with Public Subnets, Private Application Subnets, Private Database Subnets, Internet Gateway, NAT Gateway, Route Tables, and Security Group chaining. | 23/06/2026 | 23/06/2026 | CloudFormation VPC |
| 24/06 | Configured Application Load Balancer, Target Group, Launch Template, Auto Scaling Group, private RDS MySQL, two S3 buckets, CloudFront OAC, SPA routing, and AWS WAF. | 24/06/2026 | 24/06/2026 | ALB/ASG/RDS/S3/CloudFront/WAF |
| 25/06 | Built the asynchronous voice-processing pipeline: Backend stores audio in S3, creates a task in RDS, sends a message to SQS, and Worker calls Amazon Transcribe with failures moved to a Dead-Letter Queue. | 25/06/2026 | 25/06/2026 | SQS/Transcribe |
| 26/06 | Integrated Amazon SES for verification and password reset emails; configured Cognito dual-auth, least-privilege IAM Roles, Secrets Manager, and Systems Manager for EC2 administration without opening SSH. | 26/06/2026 | 26/06/2026 | SES/Cognito/IAM/Secrets/SSM |
| 27/06 | Completed project documents including Proposal, Worklog, AWS deployment Workshop, library list, testing checklist, and draw.io AWS architecture guidance using AWS Architecture icons. | 27/06/2026 | 27/06/2026 | Project Documentation |

## Week 11 Achievements & Learnings:

* Mapped the VDCMS system to a production-oriented AWS architecture.
* Prepared infrastructure automation and secure operational patterns for deployment.
* Completed the asynchronous Vietnamese voice transcription design and supporting project documentation.

