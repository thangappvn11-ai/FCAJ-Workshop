---
title: "Xác thực trạng thái tài nguyên sau triển khai"
date: 2026-07-08
weight: 5
chapter: false
pre: " <b> 5.5 </b> "
---

#### Mục đích

Xác minh các dịch vụ cốt lõi đã được CloudFormation thiết lập đúng kiến trúc tiêu chuẩn bảo mật.


#### Các dịch vụ cần xác thực thực tế trên AWS Dashboard

| Tên dịch vụ | Tên tài nguyên thực tế tạo ra | Trạng thái hoạt động |
| --- | --- | --- |
| Amazon S3 | `vdcms-prod-frontendbucket-eltg7i7qejwf` (Frontend) & `vdcms-prod-databucket-fd8alu3qezcr` (Data) | Created |
| Amazon EC2 | `vdcms-prod-backend` (t3.small) | Running |
| Amazon RDS | `vdcms-prod-database` (Engine: MySQL Community / Aurora) | Available |
| Amazon CloudFront | Phân phối `E1R0G8NW5808J0` | Enabled |
| Amazon SQS | `vdcms-prod-TranscribeQueue` & `vdcms-prod-TranscribeDeadLetterQueue` | Created |
| Amazon Cognito | User pool `vdcms-prod-users` với các nhóm: `admin`, `manager`, `engineer` | Created |
| AWS Secrets Manager | `DatabaseServer-Secret...`, `JwtSecret-...` | Stored securely |
| AWS WAF & Shield | `vdcms-prod-regional-waf` (4 bộ luật) | Applied |
| Amazon CloudWatch | Log groups RDS (lịch sử tham số TLS) | Available |

* **Amazon S3:** Xác nhận hai bucket đã được tạo thành công: `vdcms-prod-frontendbucket-eltg7i7qejwf` chứa mã nguồn Frontend tĩnh và `vdcms-prod-databucket-fd8alu3qezcr` lưu trữ file ghi âm và file tạm của Amazon Transcribe.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-3.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon EC2:** Kiểm tra mục **Instances**, xác nhận máy chủ `vdcms-prod-backend` (loại cấu hình t3.small) đang ở trạng thái **Running**.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon RDS:** Truy cập cấu hình quản lý Aurora và RDS, kiểm tra phần **Databases** để đảm bảo cụm cơ sở dữ liệu `vdcms-prod-database` (sử dụng Engine MySQL Community) đang hoạt động ổn định ở trạng thái **Available**.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-5.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon CloudFront:** Xác nhận ID phân phối `E1R0G8NW5808J0` đang bật ở chế độ **Enabled** để phục vụ định tuyến nội dung.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-2.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon SQS:** Đảm bảo hai hàng đợi `vdcms-prod-TranscribeQueue` và hàng đợi thư chết `vdcms-prod-TranscribeDeadLetterQueue` được khởi tạo thành công để xử lý các tác vụ xử lý âm thanh bất đồng bộ.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-4.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon Cognito:** Kiểm tra cụm **User pools**, xác nhận có sự xuất hiện của nhóm `vdcms-prod-users`. Đi sâu vào mục cấu hình **Groups**, hệ thống IaC đã tự động tạo sẵn 3 nhóm quyền cốt lõi cho ứng dụng: `admin`, `manager`, và `engineer`.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-8.png" style="max-width:100%; margin-bottom:16px;" />

* **AWS Secrets Manager:** Đảm bảo hệ thống đang lưu trữ các chuỗi khóa bí mật như thông tin tài khoản Database (`DatabaseServer-Secret...`) hay khóa ký mã thông báo JWT (`JwtSecret-...`) một cách an toàn.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-7.png" style="max-width:100%; margin-bottom:16px;" />

* **AWS WAF & Shield:** Kiểm tra phân vùng ACL ứng dụng web Regional, xác thực chính sách tường lửa ứng dụng `vdcms-prod-regional-waf` đang áp dụng cấu hình bảo mật trực tiếp, bao gồm 4 bộ luật xử lý: ngăn chặn các hành vi giả mạo, kiểm soát IP xấu, giới hạn tần suất yêu cầu trên mỗi IP và quét mã độc hại.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-6.png" style="max-width:100%; margin-bottom:16px;" />

* **Amazon CloudWatch:** Truy cập mục **Log groups**, đi sâu vào dòng nhật ký sự kiện của RDS để kiểm tra toàn bộ lịch sử truy vấn khởi tạo, cấu hình tham số bảo mật kết nối TLS của MySQL.

<img src="/images/5-Workshop/5.5-Multi-Service-Integration/5-5-1.png" style="max-width:100%; margin-bottom:16px;" />