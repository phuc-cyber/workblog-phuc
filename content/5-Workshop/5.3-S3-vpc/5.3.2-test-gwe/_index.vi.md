---
title: "Kiểm tra CloudFormation và output"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

# Kiểm tra CloudFormation và output

## CloudFormation

1. Mở AWS Console và chọn Region **Asia Pacific (Singapore)**.
2. Vào **CloudFormation → Stacks**.
3. Mở `SmartParkingWorkshopDatabaseStack`.
4. Kiểm tra trạng thái thành công và các tab **Resources**, **Events**, **Outputs**.
5. Làm tương tự với `SmartParkingWorkshopServicesStack`.

Database stack phải có:

- RDS PostgreSQL instance.
- Security Group chỉ mở cho CIDR được cấu hình.
- Secret do RDS quản lý.

Services stack phải có:

- S3 bucket lưu ảnh, chặn public access và bắt buộc HTTPS.
- Cognito User Pool, App Client và hai group `USER`/`ADMIN`.
- Lambda Python 3.12 cho camera vị trí.
- IAM policy giới hạn quyền truy cập đúng bucket, Lambda và User Pool.

## Ghi lại output an toàn

Các giá trị được phép dùng để cấu hình backend:

- AWS Region.
- Tên S3 bucket.
- Cognito User Pool ID và App Client ID.
- Tên Lambda phát hiện vị trí đỗ.
- RDS endpoint, port, database name và **ARN của secret**.

{{% notice warning %}}
Không đưa giá trị password bên trong secret vào báo cáo hoặc ảnh chụp. Với Secrets Manager, chỉ cần chứng minh secret tồn tại và ứng dụng tham chiếu đúng ARN.
{{% /notice %}}

## Kết quả mong đợi

- Hai stack ở trạng thái thành công.
- RDS có trạng thái `Available`.
- S3, Cognito và Lambda xuất hiện ở đúng Region.
- Không có API Gateway hoặc CloudFront trong chế độ `services-only`; đây là thiết kế hiện tại, không phải lỗi deploy.
