---
title: "Worklog Tuần 5"
date: 2026-05-11
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

{{% notice note %}}
**Thông tin:** Báo cáo tiến độ tuần 5 - học quản trị đa tài khoản, bảo mật thông tin nhạy cảm, IaC, container và pipeline triển khai.
{{% /notice %}}

<!-- Removed duplicate title -->

## Mục tiêu Tuần 5:

* Hiểu quản trị tài khoản tập trung với AWS Organizations và SCP.
* Thực hành quản lý secret, certificate và tự động hóa hạ tầng bằng Terraform.
* Nắm khái niệm container và CI/CD trong triển khai ứng dụng Cloud.

## Công việc thực hiện trong tuần:

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 11/05 | Nghiên cứu dịch vụ quản lý tài khoản doanh nghiệp AWS Organizations; thực hành tạo tài khoản thành viên, cấu hình phân hệ kiểm soát chính sách bảo mật (Service Control Policies - SCPs) để quản lý tập trung đa tài khoản. | 11/05/2026 | 11/05/2026 | AWS Organizations |
| 12/05 | Tìm hiểu dịch vụ AWS Secrets Manager và Certificate Manager (ACM); thực hành khởi tạo, quản lý vòng đời chứng chỉ SSL/TLS miễn phí và cấu hình cơ chế tự động xoay vòng (rotation) mật khẩu database bảo mật. | 12/05/2026 | 12/05/2026 | Secrets Manager/ACM |
| 13/05 | Tiếp cận xu hướng Cơ sở hạ tầng bằng mã (IaC) thông qua công cụ HashiCorp Terraform; học cú pháp ngôn ngữ HCL cơ bản, cách khởi tạo file cấu hình (main.tf, variables.tf) để quản lý tài nguyên AWS. | 13/05/2026 | 13/05/2026 | Terraform |
| 14/05 | Thực hành triển khai Lab Terraform nâng cao; viết script tự động hóa khởi tạo toàn bộ hạ tầng mạng VPC, máy chủ EC2 và cơ sở dữ liệu RDS chỉ bằng một câu lệnh, tìm hiểu cách quản lý file trạng thái (terraform.tfstate). | 14/05/2026 | 14/05/2026 | Terraform Lab |
| 15/05 | Tìm hiểu giải pháp Container hóa trên AWS với hai dịch vụ Amazon ECS (Elastic Container Service) và Amazon EKS (Elastic Kubernetes Service); thực hành đóng gói một ứng dụng Web cơ bản thành Docker Image. | 15/05/2026 | 15/05/2026 | ECS/EKS Docker |
| 16/05 | Thiết lập hệ thống CI/CD cơ bản sử dụng bộ công cụ AWS Developer Tools (CodeCommit, CodeBuild, CodePipeline); thực hành cấu hình tự động đẩy mã nguồn từ kho lưu trữ lên môi trường chạy thử nghiệm và hoàn thiện báo cáo tuần. | 16/05/2026 | 16/05/2026 | AWS Developer Tools |

## Kết quả & kiến thức đạt được Tuần 5:

* Hiểu vai trò của governance, security và automation trong môi trường Cloud chuyên nghiệp.
* Thực hành tự động hóa hạ tầng và nắm tầm quan trọng của file trạng thái Terraform.
* Liên kết container và pipeline CI/CD với quy trình triển khai ứng dụng.

