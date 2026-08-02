---
title: "Worklog Tuần 3"
date: 2026-07-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

# Tuần 3: Xây dựng xác thực và phân quyền User/Admin

**Thời gian:** 25/05/2026 - 31/05/2026

## Mục tiêu

- Hoàn thiện luồng đăng ký, đăng nhập và xác minh tài khoản.
- Tách quyền truy cập giữa User và Admin.
- Chuẩn bị chế độ local-auth và khả năng tích hợp Cognito.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 25/05/2026 | Thiết kế dữ liệu tài khoản và vai trò người dùng. |
| 26/05/2026 | Xây dựng luồng đăng nhập và xử lý trạng thái phiên. |
| 27/05/2026 | Áp dụng kiểm tra role cho các API User và Admin. |
| 28/05/2026 | Chuẩn bị Cognito User Pool với nhóm `USER` và `ADMIN`. |
| 29/05/2026 | Kiểm thử đăng nhập hợp lệ, sai mật khẩu và truy cập sai quyền. |

## Kết quả

- Người dùng được chuyển đến đúng không gian làm việc theo role.
- API từ chối các yêu cầu không có quyền phù hợp.
- Hệ thống có thể chạy local-auth và sẵn sàng chuyển sang Cognito.

## Liên hệ sản phẩm

Màn hình đăng nhập và hai menu User/Admin phản ánh trực tiếp kết quả của tuần này.
