---
title: "Worklog Tuần 12"
date: 2026-06-29
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

<!-- Removed duplicate title -->
{{% notice note %}}
**Thông tin:** Báo cáo cuối tuần 12 - validate, deploy, test, cải tiến, dọn dẹp và hoàn thiện VDCMS trên AWS.
{{% /notice %}}

**Thời gian:** 29/06/2026 - 04/07/2026

## Mục tiêu Tuần 12:

* Validate và triển khai stack CloudFormation của VDCMS.
* Kiểm thử toàn bộ hệ thống qua CloudFront và xử lý các lỗi triển khai.
* Hoàn thiện tài liệu, sơ đồ kiến trúc, rà soát chi phí, cleanup và nội dung thuyết trình.

## Công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 29/06 | Kiểm tra CloudFormation bằng validate-template và Change Set; rà soát quyền IAM cần thiết để CloudFormation tạo Role, Instance Profile và truyền Role cho EC2/Transcribe. | 29/06/2026 | 29/06/2026 | CloudFormation Validation |
| 30/06 | Triển khai stack VDCMS trên AWS; phân tích sự kiện rollback, sửa các thuộc tính Cognito/CloudFront không tương thích và hoàn thiện quy trình dọn các tài nguyên được giữ lại sau khi stack lỗi. | 30/06/2026 | 30/06/2026 | AWS Stack Deployment |
| 01/07 | Triển khai lại stack thành công; kiểm tra Target Group healthy, kết nối RDS, API health check; build Frontend, đồng bộ lên S3 và tạo CloudFront invalidation để cập nhật phiên bản mới. | 01/07/2026 | 01/07/2026 | CloudFront/S3/RDS/API |
| 02/07 | Kiểm thử end-to-end trên domain CloudFront; cấu hình SES identity và Sandbox, xử lý lỗi WAF chặn multipart upload, sửa quyền S3 cho Transcribe và xác nhận chuyển giọng nói tiếng Việt thành văn bản thành công. | 02/07/2026 | 02/07/2026 | E2E Testing |
| 03/07 | Chuẩn hóa môi trường EC2 lên Node.js 20; cấu hình và kiểm tra hai systemd service cho Backend API và SQS Transcribe Worker; triển khai các cải tiến giao diện báo cáo, checklist và modal chi tiết. | 03/07/2026 | 03/07/2026 | EC2 systemd |
| 04/07 | Kiểm thử toàn bộ luồng Admin, Manager và Engineer; rà soát chi phí, xóa stack cùng bucket S3, snapshot RDS và SES identity sau khi kiểm thử; hoàn thiện sơ đồ kiến trúc AWS và nội dung thuyết trình dự án. | 04/07/2026 | 04/07/2026 | Final Cleanup |

## Kết quả & kiến thức đạt được Tuần 12:

* Triển khai và xác minh thành công stack VDCMS trên AWS sau khi xử lý rollback.
* Xác nhận luồng người dùng đầy đủ và pipeline chuyển giọng nói tiếng Việt trong kiến trúc AWS.
* Hoàn tất rà soát chi phí, dọn tài nguyên, sơ đồ kiến trúc và nội dung thuyết trình cuối kỳ.

