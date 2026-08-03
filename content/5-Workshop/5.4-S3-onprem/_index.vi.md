---
title: "Chạy và demo hệ thống"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Phần này kết nối ứng dụng local với các dịch vụ AWS đã triển khai, sau đó thực hiện luồng nghiệp vụ hoàn chỉnh.

```text
User đặt vị trí → hệ thống tạo booking + QR PENDING
       ↓
Admin quét QR và lưu ảnh cổng vào → nhập biển số → ACTIVE
       ↓
Camera vị trí → S3 → Lambda → Rekognition → kết quả quan sát
       ↓
Admin quét lại QR tại cổng ra → ảnh mới + so khớp biển số → CLOSED
```

![Tổng quan trung tâm vận hành Smart Parking](/images/5-Workshop/04-admin-overview.png)

*Hình 5.4-1: Trung tâm vận hành giúp Admin theo dõi chỗ trống, phiên gửi xe, cảnh báo và doanh thu mô phỏng.*

## Các bước

1. [Khởi động backend và frontend](5.4.1-prepare/)
2. [User đặt chỗ và nhận QR](5.4.2-create-interface-enpoint/)
3. [Admin check-in và check-out](5.4.3-test-endpoint/)
4. [AI giám sát vị trí đỗ](5.4.4-dns-simulation/)

{{% notice info %}}
Ứng dụng hỗ trợ cả storage local và S3. Để tạo bằng chứng AWS trong Workshop, hãy chạy backend với file cấu hình AWS và xác nhận `Storage Provider = s3` trên trang cấu hình Admin.
{{% /notice %}}
