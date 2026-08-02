---
title: "Worklog Tuần 9"
date: 2026-07-01
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

# Tuần 9: Phân tích vị trí đỗ bằng Lambda và Rekognition

**Thời gian:** 06/07/2026 - 12/07/2026

## Mục tiêu

- Tự động xác định vị trí có xe hoặc không có xe từ ảnh camera.
- Tách tác vụ AI khỏi FastAPI bằng AWS Lambda.
- Lưu kết quả phân tích và log phục vụ kiểm tra.

## Công việc thực hiện

| Ngày | Nội dung |
| --- | --- |
| 06/07/2026 | Xác định input, output và ngưỡng confidence cho tác vụ nhận diện. |
| 07/07/2026 | Xây dựng Lambda đọc ảnh từ S3 và gọi Amazon Rekognition. |
| 08/07/2026 | Chuẩn hóa kết quả về trạng thái có xe hoặc không có xe. |
| 09/07/2026 | Kết nối kết quả AI với trạng thái vị trí trong backend. |
| 10/07/2026 | Kiểm thử nhiều ảnh và theo dõi lỗi thực thi trong CloudWatch Logs. |

## Kết quả

- Lambda phân tích được ảnh camera mà không làm nặng FastAPI.
- Kết quả Rekognition được chuyển thành trạng thái đơn giản cho nghiệp vụ.
- CloudWatch lưu đủ log để kiểm tra lỗi và thời gian xử lý.

## Liên hệ sản phẩm

AI chỉ hỗ trợ giám sát vị trí đỗ; biển số tại cổng vẫn do Admin quan sát và nhập.
