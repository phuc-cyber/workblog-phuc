---
title: "Kiểm tra dữ liệu và giám sát"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Một demo thành công cần chứng minh được luồng từ giao diện đến AWS, không chỉ dừng ở việc trang web hiển thị đúng.

## 1. Amazon S3

Mở bucket ảnh → **Objects**:

- `plates/`: ảnh tại cổng vào và cổng ra.
- `qrcodes/`: ảnh QR được tạo cho các lượt đặt chỗ.
- `smoke-tests/`: object dùng để kiểm tra nhanh luồng triển khai.
- `slot-observations/`: ảnh camera vị trí đỗ, được tạo sau khi chạy demo AI.

Bucket phải tiếp tục bật **Block public access**. Ứng dụng upload/xem ảnh bằng presigned URL có thời hạn.

![Các thư mục object trong S3 của Smart Parking](/images/5-Workshop/aws-03-s3-objects.png)

*Hình 5.5-1: Bucket đã triển khai hiện có các thư mục bằng chứng cho biển số, mã QR và smoke test.*

## 2. Amazon RDS PostgreSQL

Trong RDS Console, kiểm tra:

- Status `Available`.
- Engine PostgreSQL.
- Kết nối database tăng khi backend hoạt động.
- PostgreSQL log không có lỗi nghiêm trọng.

RDS Console không hiển thị trực tiếp từng row. Kết nối bằng pgAdmin, DBeaver hoặc `psql` với SSL rồi chạy các câu lệnh chỉ đọc:

```sql
SELECT id, user_id, slot_id, start_time, end_time, status
FROM bookings ORDER BY id DESC LIMIT 20;

SELECT id, booking_id, entry_plate_number, exit_plate_number,
       entry_image_key, exit_image_key, status, match_result
FROM parking_sessions ORDER BY id DESC LIMIT 20;

SELECT id, event_type, booking_id, plate_number, image_key, decision, created_at
FROM gate_events ORDER BY id DESC LIMIT 20;

SELECT id, slot_id, image_key, occupied, confidence, observed_at
FROM slot_observations ORDER BY id DESC LIMIT 20;
```

Đối chiếu `image_key` trong RDS với object key tương ứng trong S3.

![Cơ sở dữ liệu PostgreSQL của Smart Parking trên RDS](/images/5-Workshop/aws-04-rds-database.png)

*Hình 5.5-2: Amazon RDS báo cơ sở dữ liệu PostgreSQL của Smart Parking đang ở trạng thái khả dụng.*

## 3. Lambda, Rekognition và CloudWatch

1. Gửi một ảnh camera vị trí ở chế độ AI.
2. Mở Lambda phát hiện vị trí → tab **Monitor**.
3. Xác nhận `Invocations` tăng và `Errors = 0` cho luồng thành công.
4. Mở CloudWatch log stream mới nhất.
5. Đối chiếu kết quả với record mới trong `slot_observations`.

Rekognition `DetectLabels` là lời gọi on-demand, vì vậy không có danh sách lịch sử ảnh riêng trên Rekognition Console.

![Các Lambda function của Smart Parking](/images/5-Workshop/aws-05-lambda-functions.png)

*Hình 5.5-3: Các Lambda function phục vụ xử lý AI và quản lý thời gian lưu CloudWatch log.*

![Các CloudWatch log group của Smart Parking](/images/5-Workshop/aws-07-cloudwatch-log-groups.png)

*Hình 5.5-4: CloudWatch nhận log từ Lambda xử lý AI, Lambda quản lý log và RDS PostgreSQL.*

## 4. Cognito

Kiểm tra User Pool, App Client và hai group `USER`/`ADMIN`. Nếu backend bật Cognito authentication, tài khoản đăng ký mới phải xuất hiện trong Users và group tương ứng. Nếu ứng dụng đang dùng local-auth, tài khoản chỉ xuất hiện trong RDS; đây là khác biệt cấu hình cần ghi rõ khi báo cáo.

![Hai group USER và ADMIN trong Cognito](/images/5-Workshop/aws-06-cognito-groups.png)

*Hình 5.5-5: Cognito User Pool có hai nhóm phân quyền `USER` và `ADMIN`.*

## 5. Checklist bằng chứng

- [ ] Hai CloudFormation stack triển khai thành công.
- [ ] RDS có trạng thái `Available` và có record của luồng demo.
- [ ] S3 có các thư mục bằng chứng mong đợi, gồm `plates/`, `qrcodes/` và `slot-observations/` sau khi chạy demo AI.
- [ ] Booking chuyển `PENDING → ACTIVE → CLOSED`.
- [ ] Slot chuyển `AVAILABLE → RESERVED → OCCUPIED → AVAILABLE`.
- [ ] Lambda có invocation mới và CloudWatch có log.
- [ ] RDS có kết quả camera vị trí tương ứng.
- [ ] Trường hợp biển số không khớp tạo `REVIEW_REQUIRED` và audit log.
- [ ] Không có secret, token hoặc password trong ảnh chụp.
- [ ] Trình bày rõ API Gateway/CloudFront chưa dùng ở chế độ `services-only`.

## 6. Kiểm tra chi phí

Mở **Billing and Cost Management → Cost Explorer**, nhóm theo Service và xem chi phí RDS, S3, Lambda, Rekognition, CloudWatch và Secrets Manager. RDS là tài nguyên chạy liên tục đáng chú ý nhất; Budget chỉ cảnh báo chứ không tự dừng dịch vụ.
