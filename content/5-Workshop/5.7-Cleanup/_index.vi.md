---
title: "Quy trình dọn dẹp giải phóng tài nguyên"
date: 2026-07-08
weight: 7
chapter: false
pre: " <b> 5.7 </b> "
---

#### Mục đích

Xóa bỏ triệt để hạ tầng thử nghiệm sau khi hoàn tất quá trình kiểm thử luồng hệ thống nhằm tối ưu hóa chi phí và tránh phát sinh hóa đơn ngoài ý muốn trên AWS.

#### Các bước tiến hành cụ thể qua AWS CloudShell

Người thực hiện mở cửa sổ tiện ích **AWS CloudShell** và lần lượt thực thi các giai đoạn dọn dẹp hạ tầng tự động sau:

**Bước 1 — Tắt tính năng RDS Deletion Protection và xóa dữ liệu trong S3 Buckets**
Do cơ sở dữ liệu và các bucket dữ liệu được thiết lập cơ chế bảo vệ nghiêm ngặt chống xóa nhầm, trước hết cần truy vấn động ID của RDS để gỡ bỏ thuộc tính `deletion-protection`, đợi hệ thống áp dụng trạng thái khả dụng, sau đó truy xuất tên các bucket từ Output của Stack để thực hiện xóa đệ quy toàn bộ dữ liệu bên trong.

```bash
# Lấy DB Instance Identifier
DB_ID=$(aws rds describe-db-instances --region ap-southeast-1 \
  --query 'DBInstances[?contains(DBInstanceIdentifier,`vdcms`)].DBInstanceIdentifier' \
  --output text)

# Tắt tính năng Deletion Protection
aws rds modify-db-instance \
  --db-instance-identifier "$DB_ID" \
  --no-deletion-protection \
  --apply-immediately \
  --region ap-southeast-1 > /dev/null

echo "Chờ RDS apply (~2 phút)..."
aws rds wait db-instance-available \
  --db-instance-identifier "$DB_ID" --region ap-southeast-1

# Lấy tên các buckets từ CloudFormation Outputs
FRONTEND_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`FrontendBucketName`].OutputValue' \
  --output text)

DATA_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`DataBucketName`].OutputValue' \
  --output text)

# Xóa đệ quy toàn bộ dữ liệu bên trong bucket
aws s3 rm "s3://$FRONTEND_BUCKET" --recursive
aws s3 rm "s3://$DATA_BUCKET" --recursive

echo "=== BƯỚC 1 XONG ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7.png" style="max-width:100%; margin-bottom:16px;" />

**Bước 2 — Xóa bỏ CloudFormation Stack**
Thực hiện lệnh xóa toàn bộ Stack hạ tầng `vdcms-prod`. Tiến trình này sẽ tự động thu hồi các tài nguyên hệ thống bao gồm cụm máy chủ EC2, ALB, tường lửa WAF, cơ sở dữ liệu và các định danh IAM liên quan.

```bash
aws cloudformation delete-stack \
  --stack-name vdcms-prod --region ap-southeast-1

echo "Đang xóa stack, chờ 10–15 phút..."
aws cloudformation wait stack-delete-complete \
  --stack-name vdcms-prod --region ap-southeast-1

echo "=== STACK ĐÃ XÓA ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7-4.png" style="max-width:100%; margin-bottom:16px;" />


**Bước 3 — Xóa bỏ các thông tin cấu hình nhạy cảm trên Secrets Manager**
Sử dụng vòng lặp để quét toàn bộ danh sách ARN của các Secret có chứa chuỗi tên dự án và thực hiện xóa vĩnh viễn không qua thời gian chờ phục hồi để tối ưu hóa cước phí.

```bash
for ARN in $(aws secretsmanager list-secrets --region ap-southeast-1 \
  --query 'SecretList[?contains(Name,`Secret`)].ARN' --output text); do
  aws secretsmanager delete-secret \
    --secret-id "$ARN" \
    --force-delete-without-recovery \
    --region ap-southeast-1
  echo "Đã xóa secret: $ARN"
done

echo "=== DỌN SẠCH XONG ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7-3.png" style="max-width:100%; margin-bottom:16px;" />

---

#### Hậu kiểm và dọn dẹp thủ công trên AWS Console

Sau khi hoàn tất luồng lệnh tự động, người thực hiện truy cập vào **AWS Management Console** để rà soát, kiểm tra và xử lý dứt điểm các tài nguyên độc lập hoặc bản lưu vết tự sinh theo danh mục hướng dẫn dưới đây:

| Dịch vụ | Vị trí thao tác | Hành động yêu cầu |
| :--- | :--- | :--- |
| **S3 buckets còn lại** | S3 → Buckets | Thực hiện **Empty** dữ liệu và nhấn **Delete** lần lượt đối với từng bucket còn sót lại. |
| **RDS Snapshots** | RDS → Snapshots | Nhấp chọn 2 bản snapshot tự động sinh ra trong quá trình xóa hạ tầng → Chọn **Actions** → Nhấn **Delete**. |

<img src="/images/5-Workshop/5.7-Cleanup/5-7-1.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.7-Cleanup/5-7-2.png" style="max-width:100%; margin-bottom:16px;" />