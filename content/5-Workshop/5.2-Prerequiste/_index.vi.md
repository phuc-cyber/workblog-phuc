---
title: "Điều kiện chuẩn bị"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

# Điều kiện chuẩn bị

## Công cụ local

- Windows 10/11.
- Git.
- Docker Desktop và Docker Compose.
- Node.js cùng npm.
- Python 3.10 trở lên cho backend và AWS CDK.
- AWS CLI và AWS CDK CLI.
- Webcam hoặc ảnh `.jpg`/`.png` để mô phỏng camera cổng và camera vị trí.

Kiểm tra nhanh:

```powershell
docker --version
node --version
npm --version
python --version
aws --version
```

## Tài khoản AWS

- Sử dụng Region `ap-southeast-1` (Singapore).
- Bật MFA cho tài khoản quản trị.
- Dùng IAM user/role dành riêng cho triển khai, không dùng root account.
- Tạo Budget Alert trước khi tạo RDS.
- Profile local được dự án sử dụng là `car-parking-deployer`.

```powershell
aws sts get-caller-identity --profile car-parking-deployer
```

## Mã nguồn và cấu hình

```text
D:\Car-Parking
├── backend/       FastAPI, SQLAlchemy, Alembic
├── frontend/      Next.js, Fluent UI
├── infra/         AWS CDK
├── docker-compose.yml
└── docker-compose.aws.yml
```

Ứng dụng cần các biến môi trường phía server cho RDS, S3, Region, Cognito và Lambda AI. Chỉ lưu chúng trong file môi trường local hoặc secret store.

{{% notice warning %}}
Không đưa database password, Access Key, Secret Key hoặc nội dung file `.env.aws.local` vào Git, frontend hay báo cáo. Frontend chỉ nhận URL API và các thông tin công khai cần thiết.
{{% /notice %}}

## Phạm vi demo

- Một cổng vào và một cổng ra.
- Một bãi xe mẫu với một số vị trí; chỉ 1–2 vị trí cần bật camera AI.
- Thanh toán được mô phỏng, không gọi VNPay, MoMo, Stripe hoặc ngân hàng.
- Frontend/backend chạy local; AWS cung cấp database, storage, identity resources và AI service.
