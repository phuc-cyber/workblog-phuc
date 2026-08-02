---
title: "Worklog Tuần 11"
date: 2026-07-01
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

# Tuần 11: Triển khai S3, Cognito, Lambda và CloudWatch

**Thời gian:** 20/07/2026 - 26/07/2026

## Mục tiêu

- Hoàn thiện services stack của Car Parking.
- Cấp IAM policy theo đúng nhu cầu của backend và Lambda.
- Kiểm tra output cùng log sau triển khai.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 20/07/2026 | Khai báo S3 bucket và cấu hình phục vụ presigned URL. |
| 21/07/2026 | Tạo Cognito User Pool, app client và nhóm USER/ADMIN. |
| 22/07/2026 | Đóng gói Lambda AI và cấu hình quyền đọc ảnh S3. |
| 23/07/2026 | Bổ sung IAM runtime policy cùng CloudWatch Log Group. |
| 24/07/2026 | Deploy services stack và xác minh CloudFormation outputs. |

## Kết quả

- S3, Cognito, Lambda và CloudWatch được triển khai bằng CDK.
- Các thành phần chỉ nhận những quyền cần thiết cho chức năng.
- Output của stack có thể được đưa vào cấu hình frontend/backend.

## Liên hệ sản phẩm

Services stack hoàn thiện phần hạ tầng AWS được mô tả trong Workshop.
