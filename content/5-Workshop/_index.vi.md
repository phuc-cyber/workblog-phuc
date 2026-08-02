---
title: "Workshop: Hệ thống bãi giữ xe thông minh"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng hệ thống bãi giữ xe thông minh trên AWS

Workshop này trình bày quá trình xây dựng và kiểm thử **Smart Parking System**: người dùng đặt trước vị trí đỗ, nhận mã QR, nhân viên cổng xác nhận xe vào/ra bằng QR và ảnh biển số, còn camera tại vị trí đỗ sử dụng AI để phát hiện có xe hoặc không có xe.

![Bãi giữ xe thông minh](/images/5-Workshop/smart-parking-hero.png)

{{% notice info %}}
Phiên bản hiện tại triển khai theo mô hình **services-only**: Next.js và FastAPI chạy trên máy local, trong khi dữ liệu và ảnh được lưu trên các dịch vụ AWS. Hệ thống không tích hợp cổng thanh toán thật; các khoản giữ chỗ, hoàn tiền và phí cuối cùng chỉ được mô phỏng trong PostgreSQL.
{{% /notice %}}

## Nội dung Workshop

1. [Tổng quan giải pháp](5.1-Workshop-overview/)
2. [Điều kiện chuẩn bị](5.2-Prerequiste/)
3. [Triển khai dịch vụ AWS](5.3-S3-vpc/)
4. [Chạy và demo hệ thống](5.4-S3-onprem/)
5. [Kiểm tra dữ liệu và giám sát](5.5-Policy/)
6. [Dọn dẹp và kiểm soát chi phí](5.6-Cleanup/)

Sau khi hoàn thành, người thực hiện có thể:

- Giải thích kiến trúc Next.js, FastAPI, RDS PostgreSQL, S3, Cognito, Lambda, Rekognition và CloudWatch.
- Chạy frontend/backend local nhưng sử dụng dữ liệu thật trên AWS.
- Thực hiện trọn vẹn luồng đặt chỗ → QR → check-in → giám sát vị trí → check-out.
- Kiểm tra bằng chứng hoạt động trên AWS Console mà không làm lộ thông tin bí mật.
