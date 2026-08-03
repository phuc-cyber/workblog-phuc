---
title: "Khởi động backend và frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Terminal 1 — Backend kết nối AWS

Đảm bảo Docker Desktop đang chạy và AWS CLI profile còn hiệu lực.

```powershell
cd D:\Car-Parking
aws sts get-caller-identity --profile car-parking-deployer
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml up -d --build api
```

Chạy migration và kiểm tra container:

```powershell
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml exec api alembic upgrade head
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml ps
```

Nếu cần tạo dữ liệu demo, truyền password qua biến môi trường riêng và không ghi password thật vào tài liệu:

```powershell
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml exec -e SEED_ADMIN_PASSWORD=YOUR_ADMIN_PASSWORD -e SEED_USER_PASSWORD=YOUR_USER_PASSWORD api python scripts/seed_workshop.py
```

Backend AWS-local sử dụng:

- API: `http://localhost:8001`
- Swagger: `http://localhost:8001/docs`
- RDS PostgreSQL thay cho container PostgreSQL local.
- S3 qua presigned URL.
- AWS Lambda cho AI camera vị trí.

## Terminal 2 — Frontend

```powershell
cd D:\Car-Parking\frontend
npm install
npm run dev
```

Mở:

- Trang đăng nhập: `http://localhost:3000`
- User workspace: `http://localhost:3000/user`
- Admin workspace: `http://localhost:3000/admin`

## Kiểm tra nhanh

1. Swagger mở được và API trả phản hồi.
2. Trang đăng nhập tải được ảnh nền và form.
3. Đăng nhập đúng role sẽ được chuyển đến workspace tương ứng.
4. Admin → Cấu hình hiển thị trạng thái RDS/S3/Lambda.

{{% notice warning %}}
Không chụp DevTools, log hoặc file môi trường nếu chúng hiển thị connection string, token hay presigned URL. Presigned URL có thời hạn và phải được xem như thông tin nhạy cảm tạm thời.
{{% /notice %}}
