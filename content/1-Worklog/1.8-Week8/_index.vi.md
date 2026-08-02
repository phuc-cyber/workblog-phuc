---
title: "Worklog Tuần 8"
date: 2026-07-01
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

# Tuần 8: Tích hợp Amazon S3 cho ảnh cổng và camera

**Thời gian:** 29/06/2026 - 05/07/2026

## Mục tiêu

- Lưu ảnh phương tiện và ảnh vị trí trong Amazon S3.
- Không truyền file lớn trực tiếp qua backend.
- Kiểm soát quyền truy cập bằng presigned URL.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 29/06/2026 | Thiết kế quy ước key cho ảnh cổng vào, cổng ra và camera vị trí. |
| 30/06/2026 | Tạo bucket, cấu hình CORS và chính sách truy cập cần thiết. |
| 01/07/2026 | Xây dựng API cấp presigned URL cho thao tác upload. |
| 02/07/2026 | Lưu metadata ảnh vào database và liên kết với phiên gửi xe. |
| 03/07/2026 | Kiểm thử URL hết hạn, sai định dạng và quyền truy cập không hợp lệ. |

## Kết quả

- Ảnh được tải trực tiếp lên S3 thông qua URL có thời hạn.
- Backend chỉ quản lý metadata và quan hệ nghiệp vụ của ảnh.
- Cấu trúc lưu trữ phân biệt rõ ảnh cổng và ảnh camera vị trí.

## Liên hệ sản phẩm

Ảnh xe trong quy trình cổng và ảnh dùng cho phân tích vị trí đều dựa trên tích hợp tuần 8.
