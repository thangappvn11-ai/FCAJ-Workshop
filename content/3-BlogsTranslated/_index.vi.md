---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

{{% notice warning %}}  
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Tại đây sẽ là phần liệt kê, giới thiệu các blogs mà các bạn đã đăng trên [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Ví dụ:

###  [Blog 1 - Amazon VPC Lattice: Tăng tốc giao tiếp giữa các microservices](3.1-Blog1/)
Bài viết tóm tắt case study của Insurance Australia Group (IAG) ứng dụng Amazon VPC Lattice để giải quyết tất toàn độ trễ cao khi các Lambda function gọi qua lại trong kiến trúc serverless. Nhờ VPC Lattice, traffic không còn phải route trực tiếp mang AWS, giúp giảm P95 latency từ 15% đến 92%.

###  [Blog 2 - Logical Replication trong Amazon RDS for PostgreSQL 18](3.2-Blog2/)
Bài viết giới thiệu các cải tiến quan trọng trong logical replication trong PostgreSQL 18 khi chạy trên Amazon RDS: hỗ trợ replicate STORED generated columns, theo dõi conflict rõ ràng hơn qua pg_stat_subscription_stats, parallel streaming mặc định để giảm replication lag, thay đổi two-phase commit linh hoạt và tự xử lý replication slot ở idle.

###  [Blog 3 - Private NAT Gateway: Giải pháp cân kiết IP của United Airlines](3.3-Blog3/)
Bài viết phân tích cách United Airlines dùng Private NAT Gateway để xử lý thiếu hụt IPv4 trong môi trường hybrid AWS khi workload cần phải scale nhanh. Bằng cách chuyển compute workload sang dải 100.64.0.0/10 (RFC 6598) và dùng Private NAT Gateway làm tầp dịch địa chỉ, United đã tách được việc scale compute khỏi sự có sẵn IP routable mà không cần thay đổi hệ thống đích.

###  [Blog 4 - Amazon EKS Version Rollbacks: Nâng cấp Kubernetes không còn "một chiều"](3.4-Blog4/)
Bài viết giới thiệu tính năng Kubernetes Version Rollbacks trên Amazon EKS — cho phép hoàn tác nâng cấp control plane trong vòng 7 ngày nếu phát hiện lỗi tương thích. Tính năng này giúp các team vận hành nâng cấp phiên bản Kubernetes với tự tin, loại bỏ rủi ro "một đi không về", đặc biệt hữu ích trong môi trường production có yêu cầu kiểm soát chặt chẽ.