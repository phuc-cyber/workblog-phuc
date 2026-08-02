---
title: "Worklog Tuần 6"
date: 2026-07-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

# Tuần 6: Xử lý check-in/check-out và đối chiếu biển số

**Thời gian:** 15/06/2026 - 21/06/2026

## Mục tiêu

- Xây dựng quy trình vận hành xe vào và ra khỏi bãi.
- Lưu ảnh phương tiện cùng biển số quan sát được.
- Ngăn thao tác cổng bị xử lý trùng.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 15/06/2026 | Thiết kế API tiếp nhận QR và xác minh booking tại cổng. |
| 16/06/2026 | Xây dựng check-in, mở phiên gửi xe và cập nhật vị trí. |
| 17/06/2026 | Bổ sung ảnh lúc vào cùng biển số do Admin nhập. |
| 18/06/2026 | Phát triển check-out, đối chiếu biển số và đóng phiên. |
| 19/06/2026 | Thêm event_id và kiểm thử khả năng chống xử lý lặp. |

## Kết quả

- Admin có thể xử lý đầy đủ xe vào và xe ra.
- Hệ thống lưu riêng ảnh lúc vào, lúc ra và biển số đã chuẩn hóa.
- Trường hợp biển số không khớp được chuyển sang quy trình cần duyệt.

## Liên hệ sản phẩm

Màn hình Điều khiển cổng là kết quả trực tiếp của các chức năng tuần 6.
