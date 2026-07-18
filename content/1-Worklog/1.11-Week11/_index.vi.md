---
title: "Worklog Tuần 11"
date: 2026-06-22
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

{{% notice note %}}
**Thông tin:** Báo cáo Cloud deployment tuần 11 - ánh xạ VDCMS lên AWS, chuẩn bị hạ tầng, xử lý giọng nói bất đồng bộ, email, identity và vận hành.
{{% /notice %}}

<!-- Removed duplicate title -->

## Mục tiêu Tuần 11:

* Thiết kế kiến trúc AWS deployment cho VDCMS.
* Tạo hạ tầng CloudFormation cho networking, compute, database, storage, CDN và WAF.
* Xây dựng voice transcription bất đồng bộ, email verification, authentication, secret và quản trị EC2.

## Công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 22/06 | Phân tích yêu cầu triển khai VDCMS lên AWS; lựa chọn mô hình CloudFront, S3, WAF, ALB, EC2, RDS, SQS, Transcribe, SES, Cognito, Secrets Manager, Systems Manager và CloudWatch phù hợp với hệ thống. | 22/06/2026 | 22/06/2026 | AWS Architecture |
| 23/06 | Xây dựng CloudFormation cho VPC trên hai Availability Zone; cấu hình Public Subnet, Private Application Subnet, Private Database Subnet, Internet Gateway, NAT Gateway, Route Table và Security Group chaining. | 23/06/2026 | 23/06/2026 | CloudFormation VPC |
| 24/06 | Cấu hình Application Load Balancer, Target Group, Launch Template và Auto Scaling Group; bổ sung RDS MySQL private, hai S3 bucket cho Frontend và dữ liệu, CloudFront OAC, SPA routing và AWS WAF. | 24/06/2026 | 24/06/2026 | ALB/ASG/RDS/S3/CloudFront/WAF |
| 25/06 | Xây dựng pipeline xử lý giọng nói bất đồng bộ: Backend lưu audio vào S3, tạo tác vụ trong RDS, gửi message vào SQS; Worker nhận message, gọi Amazon Transcribe và chuyển tác vụ lỗi sang Dead-Letter Queue. | 25/06/2026 | 25/06/2026 | SQS/Transcribe |
| 26/06 | Tích hợp Amazon SES cho email xác thực và quên mật khẩu; cấu hình Cognito dual-auth, IAM Role theo nguyên tắc đặc quyền tối thiểu, Secrets Manager cho thông tin nhạy cảm và Systems Manager để quản trị EC2 không mở SSH. | 26/06/2026 | 26/06/2026 | SES/Cognito/IAM/Secrets/SSM |
| 27/06 | Hoàn thiện tài liệu dự án gồm Proposal, Worklog, Workshop triển khai AWS, danh sách thư viện, checklist kiểm thử và hướng dẫn vẽ kiến trúc hệ thống trên draw.io bằng bộ icon AWS Architecture. | 27/06/2026 | 27/06/2026 | Project Documentation |

## Kết quả & kiến thức đạt được Tuần 11:

* Ánh xạ VDCMS sang kiến trúc AWS có định hướng production.
* Chuẩn bị hạ tầng tự động và mô hình vận hành an toàn.
* Hoàn thiện thiết kế chuyển giọng nói tiếng Việt thành văn bản và tài liệu hỗ trợ dự án.

