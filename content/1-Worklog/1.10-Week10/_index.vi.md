---
title: "Worklog Tuần 10"
date: 2026-07-01
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

# Tuần 10: Triển khai PostgreSQL trên Amazon RDS bằng CDK

**Thời gian:** 13/07/2026 - 19/07/2026

## Mục tiêu

- Chuyển cơ sở dữ liệu từ local sang Amazon RDS PostgreSQL.
- Bảo vệ thông tin kết nối bằng Secrets Manager.
- Khai báo database stack bằng AWS CDK.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 13/07/2026 | Rà soát migration, dữ liệu demo và yêu cầu phiên bản PostgreSQL. |
| 14/07/2026 | Khai báo RDS, Security Group và secret trong CDK. |
| 15/07/2026 | Chạy CDK synth/diff và kiểm tra CloudFormation template. |
| 16/07/2026 | Triển khai database stack và lấy endpoint từ output. |
| 17/07/2026 | Chạy migration, nạp demo data và kiểm thử FastAPI kết nối RDS. |

## Kết quả

- Amazon RDS PostgreSQL hoạt động với Security Group được giới hạn.
- Thông tin xác thực được lưu trong Secrets Manager.
- Backend đọc và ghi dữ liệu trên RDS thành công.

## Liên hệ sản phẩm

RDS trở thành nguồn dữ liệu chính cho user, booking, QR, session, phí và audit log.
