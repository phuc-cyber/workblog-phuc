---
title: "Bản đề xuất"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Đề xuất xây dựng hệ thống Car Parking trên AWS

## 1. Tên dự án

**Xây dựng hệ thống quản lý bãi đỗ xe thông minh Car Parking kết hợp dịch vụ AWS**

Dự án hướng tới một nền tảng web hỗ trợ người dùng đặt trước vị trí đỗ xe và giúp quản trị viên theo dõi hoạt động của bãi xe. Hệ thống gồm giao diện Next.js, API FastAPI, cơ sở dữ liệu PostgreSQL, mã QR theo booking và các dịch vụ AWS dành cho xác thực, lưu trữ ảnh, giám sát và phân tích trạng thái vị trí đỗ.

## 2. Bối cảnh lựa chọn

Việc quản lý bãi xe bằng vé giấy và thao tác thủ công gây khó khăn khi cần kiểm tra vị trí còn trống, đối chiếu xe ra/vào hoặc tra cứu lịch sử. Người dùng cũng không thể chủ động chọn vị trí và thời gian gửi xe trước khi đến bãi.

Car Parking được đề xuất nhằm số hóa quy trình này bằng một ứng dụng có hai vai trò **User** và **Admin**. Dự án đồng thời tạo điều kiện thực hành cách kết nối một hệ thống web với **Amazon RDS**, **Amazon S3**, **Amazon Cognito**, **AWS Lambda**, **Amazon Rekognition**, **CloudWatch**, **AWS CDK** và **CloudFormation**.

## 3. Mục tiêu thực hiện

- Xây dựng giao diện riêng cho người dùng và quản trị viên bằng **Next.js + Fluent UI**.
- Cho phép người dùng chọn bãi xe, thời gian và vị trí còn trống.
- Tạo booking cùng mã QR có vòng đời `PENDING`, `ACTIVE`, `CLOSED`, `CANCELLED` hoặc `EXPIRED`.
- Hỗ trợ Admin xử lý check-in/check-out bằng QR, ảnh xe và biển số nhập từ ảnh quan sát.
- Hiển thị sơ đồ vị trí theo trạng thái `AVAILABLE`, `RESERVED` và `OCCUPIED`.
- Lưu user, booking, QR, phiên gửi xe, phí và audit log trong **PostgreSQL/Amazon RDS**.
- Lưu ảnh cổng và ảnh camera vị trí bằng **Amazon S3** thông qua presigned URL.
- Sử dụng **Lambda + Rekognition** để nhận biết vị trí có xe hoặc không có xe.
- Quản lý xác thực bằng **Amazon Cognito** và theo dõi log qua **CloudWatch**.
- Khai báo hạ tầng bằng **AWS CDK**, sau đó triển khai bằng CloudFormation.

## 4. Phạm vi đề tài

Phạm vi hiện tại tập trung vào phiên bản workshop/demo. Người dùng có thể đăng nhập, xem sơ đồ bãi xe, chọn thời gian, đặt vị trí, nhận QR và xem lịch sử. Admin có thể theo dõi dashboard, vận hành cổng, xem xe đang gửi, xử lý trường hợp cần duyệt, kiểm tra audit log và báo cáo phí mô phỏng.

Rekognition chỉ được sử dụng để phát hiện trạng thái có xe tại vị trí camera theo dõi. Phiên bản hiện tại chưa tự động OCR biển số; Admin đọc ảnh tại cổng và nhập biển số, sau đó backend chuẩn hóa và đối chiếu dữ liệu lúc vào/ra. Hệ thống cũng chưa tích hợp cổng thanh toán thật.

## 5. Kiến trúc đề xuất

Người dùng hoặc Admin truy cập giao diện Next.js từ trình duyệt. Frontend gửi request đến FastAPI để xác thực quyền và xử lý nghiệp vụ. FastAPI đọc/ghi dữ liệu tại PostgreSQL hoặc Amazon RDS, đồng thời tạo presigned URL để tải ảnh lên S3. Khi cần kiểm tra vị trí đỗ, Lambda nhận sự kiện ảnh và sử dụng Rekognition để trả về kết quả có xe hoặc không có xe. Cognito quản lý tài khoản và nhóm quyền; CloudWatch thu thập log và metric của các dịch vụ AWS.

![Kiến trúc hệ thống Car Parking theo AWS](/images/5-Workshop/smart-parking-architecture-aws.png)

<p style="text-align:center;"><em>Kiến trúc tổng thể của hệ thống Car Parking</em></p>

## 6. Thành phần và công nghệ

| Thành phần | Vai trò |
|---|---|
| **Next.js + Fluent UI** | Xây dựng giao diện và không gian làm việc riêng cho User/Admin. |
| **FastAPI** | Cung cấp API, kiểm tra quyền và điều phối các quy trình nghiệp vụ. |
| **PostgreSQL / Amazon RDS** | Lưu tài khoản, booking, QR, phiên gửi xe, phí và audit log. |
| **Amazon S3** | Lưu ảnh phương tiện tại cổng và ảnh camera của vị trí đỗ. |
| **Amazon Cognito** | Quản lý User Pool cùng hai nhóm quyền `USER` và `ADMIN`. |
| **AWS Lambda + Rekognition** | Phân tích ảnh camera để xác định vị trí đang trống hay có xe. |
| **CloudWatch** | Theo dõi log và metric của Lambda cùng RDS. |
| **AWS CDK + CloudFormation** | Mô tả, kiểm tra và triển khai hạ tầng theo dạng Infrastructure as Code. |

## 7. Thiết kế chức năng

### 7.1 Giao diện người dùng

Sau khi đăng nhập, người dùng chọn bãi xe, thời điểm đến, thời lượng gửi và một vị trí còn trống trên sơ đồ. Khi booking được tạo, hệ thống cung cấp QR để sử dụng tại cổng và cho phép theo dõi phiên gửi xe.

<p class="workshop-img"><img src="/images/5-Workshop/01-login.png" alt="Màn hình đăng nhập Car Parking" /></p>
<p style="text-align:center;"><em>Giao diện đăng nhập của hệ thống Car Parking</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/02-user-booking.png" alt="Người dùng chọn vị trí và tạo booking" /></p>
<p style="text-align:center;"><em>Màn hình đặt chỗ và chọn vị trí đỗ xe</em></p>

### 7.2 Giao diện quản trị

Admin sử dụng dashboard để theo dõi số vị trí còn trống, xe đang gửi, trường hợp biển số cần duyệt, vi phạm vị trí và doanh thu mô phỏng. Các màn hình vận hành hỗ trợ quét QR, xử lý cổng, kiểm tra sơ đồ bãi xe và xem báo cáo.

<p class="workshop-img"><img src="/images/5-Workshop/04-admin-overview.png" alt="Dashboard tổng quan dành cho Admin" /></p>
<p style="text-align:center;"><em>Dashboard vận hành bãi xe theo thời gian thực</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/05-admin-gate-control.png" alt="Màn hình điều khiển cổng Car Parking" /></p>
<p style="text-align:center;"><em>Chức năng xử lý check-in và check-out tại cổng</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/06-admin-parking-map.png" alt="Sơ đồ vị trí đỗ xe dành cho Admin" /></p>
<p style="text-align:center;"><em>Sơ đồ giúp Admin quan sát trạng thái từng vị trí</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/07-admin-revenue.png" alt="Báo cáo doanh thu mô phỏng" /></p>
<p style="text-align:center;"><em>Màn hình tổng hợp phí và doanh thu mô phỏng</em></p>

### 7.3 Backend và dữ liệu

FastAPI kiểm soát quyền, tạo booking, phát hành QR, mở hoặc đóng phiên gửi xe và ghi audit log. Mỗi thao tác tại cổng sử dụng một `event_id` duy nhất để hạn chế xử lý trùng. PostgreSQL lưu trạng thái nghiệp vụ và tính phí mô phỏng dựa trên thời gian gửi xe.

![Vòng đời booking và phiên gửi xe](/images/5-Workshop/smart-parking-flow.svg)

### 7.4 Dịch vụ AWS

Hạ tầng workshop sử dụng một stack database cho RDS PostgreSQL, Security Group và Secrets Manager; stack dịch vụ còn lại chứa S3, Cognito, Lambda AI, IAM policy và CloudWatch Logs. Frontend cùng FastAPI hiện có thể chạy local hoặc trên VPS trong khi vẫn kết nối các dịch vụ AWS này.

![Mô hình triển khai dịch vụ AWS](/images/5-Workshop/smart-parking-deployment-aws.png)

## 8. Kế hoạch triển khai

| Giai đoạn | Nội dung |
|---|---|
| Giai đoạn 1 | Rà soát mã nguồn frontend, backend, database migration và dữ liệu demo. |
| Giai đoạn 2 | Chuẩn bị AWS CLI, CDK, IAM profile và biến môi trường triển khai. |
| Giai đoạn 3 | Triển khai RDS PostgreSQL, Security Group và Secrets Manager. |
| Giai đoạn 4 | Tạo S3, Cognito, Lambda AI, IAM policy và CloudWatch Logs. |
| Giai đoạn 5 | Chạy migration, nạp dữ liệu demo và kết nối FastAPI với RDS. |
| Giai đoạn 6 | Khởi động frontend, kiểm tra luồng User/Admin, booking, QR và vận hành cổng. |
| Giai đoạn 7 | Kiểm thử camera vị trí, audit log, báo cáo phí và dọn dẹp tài nguyên không cần thiết. |

## 9. Kết quả kỳ vọng

- Người dùng đăng nhập, chọn thời gian và đặt được vị trí còn trống.
- Hệ thống phát hành QR và quản lý đúng vòng đời booking.
- Admin theo dõi dashboard, xử lý xe vào/ra và kiểm tra biển số.
- Sơ đồ bãi xe phản ánh các trạng thái trống, đã đặt và đang có xe.
- Hình ảnh được lưu trong S3 và dữ liệu nghiệp vụ được ghi vào PostgreSQL/RDS.
- Lambda và Rekognition hỗ trợ phát hiện trạng thái vị trí qua ảnh camera.
- Audit log, lịch sử gửi xe và phí mô phỏng có thể được tra cứu.
- Hạ tầng được triển khai lặp lại bằng CDK và CloudFormation.

## 10. Rủi ro và hướng xử lý

| Rủi ro | Hướng xử lý |
|---|---|
| Frontend không gọi được FastAPI | Kiểm tra URL API, CORS, trạng thái backend và cấu hình mạng. |
| Backend không kết nối được PostgreSQL/RDS | Kiểm tra connection string, secret, Security Group và migration. |
| QR bị xử lý nhiều lần | Sử dụng `event_id`, kiểm tra trạng thái booking và cơ chế idempotency. |
| Ảnh không tải được lên S3 | Kiểm tra bucket, presigned URL, IAM policy và thời hạn URL. |
| Rekognition trả kết quả không chính xác | Cải thiện góc camera, chất lượng ảnh và ngưỡng confidence. |
| Chi phí AWS phát sinh ngoài dự kiến | Theo dõi Billing/Budgets và dọn dẹp tài nguyên sau khi thực hành. |

## 11. Hướng phát triển

Trong tương lai, frontend và FastAPI có thể được triển khai hoàn toàn lên AWS thay vì chạy local/VPS. Hệ thống cũng có thể bổ sung OCR biển số, thông báo theo thời gian thực, thanh toán điện tử, nhiều bãi xe, chính sách tính phí linh hoạt và pipeline CI/CD tự động.
