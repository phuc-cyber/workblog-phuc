---
title: "Admin check-in và check-out"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Check-in tại cổng vào

1. Đăng nhập role `ADMIN`.
2. Mở tab **Điều khiển cổng** và chọn **Cổng vào**.
3. Quét QR từ màn hình User hoặc nhập token demo.
4. Mở webcam/chọn ảnh và chụp biển số lúc vào.
5. Admin đọc biển số trên ảnh rồi nhập vào form.
6. Bấm **Xử lý check-in**.

![Giao diện Admin điều khiển cổng](/images/5-Workshop/05-admin-gate-control.png)

*Hình 5.4.3-1: Admin quét QR, chụp ảnh biển số và xử lý check-in hoặc check-out tại cổng.*

Ứng dụng thực hiện:

- Kiểm tra QR đang ở trạng thái `PENDING` và còn hiệu lực.
- Xin presigned URL, upload ảnh vào S3 prefix `plates/`.
- Chuẩn hóa biển số bằng cách chuyển thành chữ hoa và bỏ ký tự phân cách.
- Tạo `parking_session` và `gate_event`.
- Chuyển booking/QR sang `ACTIVE` và vị trí sang `OCCUPIED`.
- Ghi thời gian check-in cùng audit log.

{{% notice warning %}}
Ảnh cổng là bằng chứng để Admin nhập và kiểm tra biển số. Workshop không gọi Rekognition OCR hoặc mô hình OCR tự động cho biển số.
{{% /notice %}}

## Check-out tại cổng ra

1. Chọn **Cổng ra**.
2. Quét lại đúng QR đã dùng khi vào.
3. Hệ thống tải thông tin phiên đang `ACTIVE`, ảnh lúc vào, vị trí và biển số đã ghi.
4. Chụp/upload ảnh mới tại cổng ra.
5. Admin nhập biển số nhìn thấy trên ảnh và xác nhận cho xe ra.

Nếu hai biển số sau khi chuẩn hóa khớp nhau:

- Session và booking chuyển sang `CLOSED`.
- QR đóng và vị trí trở lại `AVAILABLE`.
- Hệ thống tính phí theo thời gian, ghi `final_fee`, `refund_amount` hoặc `additional_amount`.
- Ảnh ra và gate event được lưu.

### Tính phí và xử lý thời gian đỗ lâu hơn dự kiến

Backend lấy khoảng thời gian từ `entry_at` đến thời điểm check-out, làm tròn lên theo giờ và luôn tính tối thiểu một giờ. Phí cuối được tính theo công thức:

```text
phí cuối = số giờ thực tế (làm tròn lên) × đơn giá mô phỏng mỗi giờ
```

- Nếu `final_fee` thấp hơn khoản giữ chỗ, phần chênh lệch được ghi vào `refund_amount`.
- Nếu xe đỗ lâu hơn làm `final_fee` cao hơn khoản giữ chỗ, phần vượt được ghi vào `additional_amount`.
- Đây là số liệu mô phỏng; hệ thống không tự động gọi cổng thanh toán hoặc thu tiền thật.
- Booking `EXPIRED` là trường hợp không đến check-in sau thời gian chờ, không phải xe đã vào bãi rồi đỗ quá giờ.

![Báo cáo doanh thu mô phỏng](/images/5-Workshop/07-admin-revenue.png)

*Hình 5.4.3-2: Trang doanh thu tổng hợp phí giữ chỗ, phí cuối, khoản hoàn và khoản thu thêm của các phiên gửi xe.*

Nếu không khớp:

- Session chuyển sang `REVIEW_REQUIRED`.
- Xe chưa được tự động xác nhận ra.
- Admin phải mở hàng đợi duyệt, đối chiếu ảnh, nhập biển số đúng và lý do xử lý.
- Mọi quyết định được ghi audit log.

## Trường hợp cần kiểm thử

| Tình huống | Kết quả mong đợi |
|---|---|
| Quét lại QR check-in đã xử lý | Không tạo session trùng |
| QR hết hạn hoặc đã hủy | Từ chối check-in |
| Biển số vào/ra khớp | Cho phép đóng session |
| Biển số không khớp | Chuyển `REVIEW_REQUIRED` |
| Admin từ chối checkout | Session vẫn hoạt động |
| Xe ở lại lâu hơn thời gian dự kiến | Tính theo thời lượng thực tế và ghi `additional_amount` nếu phí vượt khoản giữ chỗ |
| Booking `PENDING` không check-in sau thời gian chờ | Booking và QR chuyển `EXPIRED`, vị trí trở lại `AVAILABLE` |
| Upload S3 lỗi | Không chuyển trạng thái nghiệp vụ một nửa |

![Luồng QR và phiên gửi xe](/images/5-Workshop/smart-parking-flow.svg)
