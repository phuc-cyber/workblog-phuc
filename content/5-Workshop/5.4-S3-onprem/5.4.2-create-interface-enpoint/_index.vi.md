---
title: "User đặt chỗ và nhận QR"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

# User đặt chỗ và nhận QR

## 1. Đăng nhập User

Mở trang `http://localhost:3000`, đăng nhập bằng tài khoản role `USER`. Khi Cognito được bật, người dùng mới phải xác minh email bằng mã OTP; trong chế độ local-auth, backend xác thực tài khoản trong PostgreSQL.

![Màn hình đăng nhập Smart Parking](/images/5-Workshop/01-login.png)

*Hình 5.4.2-1: Giao diện đăng nhập phân quyền User và Admin của hệ thống Smart Parking.*

## 2. Chọn thời gian và vị trí

1. Mở tab **Đặt chỗ**.
2. Chọn bãi xe và thời gian đến.
3. Chọn thời lượng gửi xe.
4. Tải lại sơ đồ để hệ thống tính các vị trí trống trong đúng khoảng thời gian.
5. Chọn một vị trí `AVAILABLE`.

Backend kiểm tra các booking trùng thời gian trước khi chấp nhận yêu cầu. Vị trí không thể được hai người đặt cùng một khoảng thời gian.

![Giao diện User chọn vị trí đỗ xe](/images/5-Workshop/02-user-booking.png)

*Hình 5.4.2-2: User chọn bãi xe, thời gian và vị trí còn trống trên sơ đồ trực quan.*

## 3. Tạo booking

Sau khi xác nhận:

- Booking được tạo ở trạng thái `PENDING`.
- Vị trí chuyển sang `RESERVED`.
- QR chứa token ngẫu nhiên gắn với booking.
- Hệ thống ghi khoản giữ chỗ mô phỏng, mặc định `20.000 VND`.
- QR chưa chứa và chưa gắn biển số.

```text
User + Slot + Time range
          ↓
Booking PENDING + simulated hold
          ↓
Opaque QR token
```

## 4. Kiểm tra kết quả

- QR hiển thị trong danh sách booking đang hoạt động.
- Lịch sử hiển thị thời gian, vị trí và trạng thái phí.
- RDS có record mới trong `bookings`, `qr_codes` và `fee_summaries`.
- S3 chưa bắt buộc có ảnh vì xe chưa đến cổng.

{{% notice info %}}
QR dùng chung cho cả check-in và check-out. Token có vòng đời và trạng thái trên server; ứng dụng không tin vào dữ liệu do client tự khai báo.
{{% /notice %}}
