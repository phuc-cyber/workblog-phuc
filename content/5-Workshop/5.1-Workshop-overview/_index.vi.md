---
title: "Tổng quan giải pháp"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Tổng quan giải pháp

## Bài toán

Bãi xe truyền thống thường phụ thuộc vào vé giấy và thao tác thủ công, khó biết chính xác vị trí nào đang trống, khó truy vết lịch sử ra/vào và dễ xảy ra sai sót khi đối chiếu xe. Dự án giải quyết bài toán này bằng một ứng dụng web có phân quyền **User/Admin**, QR theo vòng đời booking và dịch vụ lưu trữ, AI trên AWS.

## Chức năng chính

### Người dùng

- Đăng ký hoặc đăng nhập tài khoản.
- Chọn bãi xe, khung thời gian và vị trí còn trống.
- Tạo booking và nhận QR ở trạng thái `PENDING`.
- Theo dõi trạng thái booking, phí giữ chỗ mô phỏng và lịch sử gửi xe.

### Quản trị viên

- Xem dashboard, số xe trong bãi và trạng thái từng vị trí.
- Quét QR, chụp ảnh xe và nhập biển số quan sát được để check-in/check-out.
- Giám sát vị trí đỗ bằng ảnh camera và AI.
- Xử lý trường hợp biển số lúc ra không khớp, xem audit log và báo cáo phí.

## Kiến trúc đang sử dụng

![Kiến trúc Smart Parking](/images/5-Workshop/smart-parking-architecture.svg)

| Thành phần | Vai trò |
|---|---|
| Next.js + Fluent UI | Giao diện User và Admin |
| FastAPI | API, phân quyền và xử lý nghiệp vụ |
| Amazon RDS PostgreSQL | Nguồn dữ liệu chính cho user, booking, QR, session, phí và log |
| Amazon S3 | Lưu ảnh biển số và ảnh camera vị trí qua presigned URL |
| Amazon Cognito | User Pool và nhóm `USER`/`ADMIN`; sẵn sàng cho chế độ xác thực AWS |
| AWS Lambda + Rekognition | Phát hiện có xe/không có xe tại vị trí đỗ |
| CloudWatch | Log và metric của Lambda/RDS |
| AWS CDK + CloudFormation | Khai báo và triển khai hạ tầng |

{{% notice warning %}}
Rekognition chỉ được dùng cho **camera vị trí đỗ**. Phiên bản hiện tại không tự động OCR biển số: Admin xem ảnh cổng và nhập biển số thủ công, sau đó backend chuẩn hóa và so khớp biển số lúc vào/ra.
{{% /notice %}}

## Vòng đời nghiệp vụ

![Luồng QR và phiên gửi xe](/images/5-Workshop/smart-parking-flow.svg)

```text
Booking/QR: PENDING → ACTIVE → CLOSED
                    ↘ CANCELLED
                    ↘ EXPIRED

Vị trí đỗ: AVAILABLE → RESERVED → OCCUPIED → AVAILABLE
```

Mỗi lần xử lý cổng có một `event_id` duy nhất để hạn chế xử lý trùng. Hệ thống lưu riêng ảnh lúc vào và lúc ra, ghi audit log cho các thay đổi trạng thái, đồng thời tính phí cuối cùng dựa trên thời gian gửi xe.
