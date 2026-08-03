---
title: "Bản đề xuất"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Đề xuất triển khai dự án Smart Parking trên AWS

## 1. Tên dự án

**Triển khai nền tảng quản lý bãi đỗ xe thông minh bằng các dịch vụ AWS**

Dự án tập trung triển khai một nền tảng web hỗ trợ đặt chỗ, vận hành cổng bằng QR, theo dõi phiên gửi xe và giám sát dành cho Admin. Hệ thống gồm frontend Next.js + Fluent UI, API nghiệp vụ FastAPI, dữ liệu PostgreSQL, ảnh bằng chứng trên S3, group Cognito và luồng phân tích trạng thái vị trí bằng AI.

## 2. Bối cảnh và động lực

Một hệ thống bãi xe thực tế không chỉ cần hiển thị danh sách vị trí mà còn phải lưu trạng thái booking, xác minh sự kiện xe vào/ra, lưu ảnh bằng chứng và cung cấp lịch sử vận hành có thể truy vết. Vé giấy và các record rời rạc khiến việc xác định vị trí còn trống, kiểm tra một sự kiện xe hoặc tìm nguyên nhân ngoại lệ trở nên khó khăn.

Việc triển khai Smart Parking với AWS minh họa cách **Amazon RDS**, **Amazon S3**, **Amazon Cognito**, **AWS Lambda**, **Amazon Rekognition**, **IAM**, **AWS CDK**, **CloudFormation** và **CloudWatch** kết hợp trong một ứng dụng thực tế. Frontend cùng FastAPI có thể chạy local hoặc trên VPS trong khi vẫn sử dụng các tài nguyên AWS được quản lý.

## 3. Mục tiêu

- Cung cấp không gian User và Admin bằng **Next.js + Fluent UI**.
- Cho phép người dùng chọn bãi xe, thời gian đến và vị trí còn trống.
- Tạo mã QR cho booking và duy trì các trạng thái `PENDING`, `ACTIVE`, `CLOSED`, `CANCELLED`, `EXPIRED`.
- Hỗ trợ Admin xử lý check-in/check-out bằng QR, ảnh tại cổng và biển số quan sát được.
- Hiển thị vị trí theo trạng thái `AVAILABLE`, `RESERVED`, `OCCUPIED`.
- Lưu user, booking, QR, phiên gửi xe, phí và audit event trong **PostgreSQL/Amazon RDS**.
- Lưu ảnh cổng và ảnh camera vị trí trong **Amazon S3** bằng presigned URL.
- Sử dụng **AWS Lambda + Amazon Rekognition** để xác định vị trí camera có xe hay không.
- Quản lý quyền bằng **Amazon Cognito** và theo dõi hoạt động AWS qua **CloudWatch**.
- Khai báo hạ tầng bằng **AWS CDK** và triển khai qua CloudFormation.

## 4. Phạm vi

Phạm vi dự án tập trung vào môi trường workshop và demo. Người dùng có thể đăng ký hoặc đăng nhập, xem sơ đồ bãi xe, chọn thời gian, đặt vị trí, nhận QR và xem lịch sử booking. Admin có thể theo dõi tình trạng vị trí, vận hành luồng cổng, xem trường hợp biển số không khớp, kiểm tra audit record và báo cáo phí mô phỏng.

Phiên bản hiện tại chưa tự động OCR biển số và chưa tích hợp thanh toán thật. Admin đọc ảnh tại cổng rồi nhập biển số quan sát được; backend chuẩn hóa và đối chiếu giá trị vào/ra. API Gateway và CloudFront chưa nằm trong chế độ triển khai `services-only` hiện tại; có thể bổ sung khi frontend và API chuyển sang mô hình hosting AWS đầy đủ.

## 5. Kiến trúc đề xuất

User và Admin truy cập ứng dụng Next.js bằng trình duyệt. Frontend gọi FastAPI để xác thực quyền và xử lý nghiệp vụ. FastAPI tạo booking/QR, cập nhật trạng thái cổng và phiên gửi xe, tính phí mô phỏng, rồi ghi audit event vào PostgreSQL/Amazon RDS. S3 lưu ảnh cổng và ảnh vị trí bằng presigned URL. Lambda phát hiện trạng thái vị trí gọi Rekognition theo yêu cầu, Cognito quản lý hai group `USER`/`ADMIN`, còn CloudWatch nhận log dịch vụ.

![Kiến trúc triển khai Car Parking trên AWS](/images/5-Workshop/smart-parking-architecture-aws.svg)

*Luồng triển khai Smart Parking bằng lớp ứng dụng local và các dịch vụ AWS được quản lý.*

## 6. Dịch vụ AWS sử dụng

| Dịch vụ | Vai trò trong dự án |
|---|---|
| **Amazon RDS PostgreSQL** | Lưu user, vị trí, booking, phiên gửi xe, phí và audit event. |
| **Amazon S3** | Lưu ảnh biển số, cổng, QR, smoke-test và camera vị trí; chặn truy cập công khai. |
| **Amazon Cognito** | Quản lý User Pool, App Client và hai group phân quyền `USER`/`ADMIN`. |
| **AWS Lambda** | Xử lý ảnh vị trí và tự động hóa thời gian lưu log. |
| **Amazon Rekognition** | Nhận diện label theo yêu cầu cho demo trạng thái vị trí. |
| **Security Group và IAM** | Giới hạn database và runtime chỉ được dùng các port, tài nguyên, action cần thiết. |
| **AWS CDK + CloudFormation** | Mô tả, xem xét và triển khai database/services stack dưới dạng mã hạ tầng. |
| **Amazon CloudWatch** | Cung cấp log group Lambda/RDS để giám sát và xử lý sự cố. |

## 7. Thiết kế thành phần

### 7.1 Frontend

Frontend được xây dựng bằng Next.js và Fluent UI. Ứng dụng cung cấp không gian User/Admin, xử lý đăng nhập, hiển thị sơ đồ bãi xe và gọi FastAPI cho các thao tác đặt chỗ/vận hành. Trong workshop, frontend có thể chạy local hoặc trên VPS nhưng vẫn kết nối tới các tài nguyên AWS.

<p class="workshop-img"><img src="/images/5-Workshop/01-login.png" alt="Màn hình đăng nhập Smart Parking" /></p>

*Giao diện xác thực của Smart Parking.*

<p class="workshop-img"><img src="/images/5-Workshop/02-user-booking.png" alt="Người dùng chọn vị trí đỗ xe" /></p>

*Luồng User chọn vị trí và tạo booking.*

### 7.2 Backend

Backend được triển khai bằng FastAPI. Backend kiểm tra quyền, tạo booking và QR, xử lý check-in/check-out tại cổng, ghi audit event và trả dữ liệu cho không gian User/Admin. Backend đọc secret database từ môi trường triển khai thay vì lưu password trong source code.

<p class="workshop-img"><img src="/images/5-Workshop/04-admin-overview.png" alt="Dashboard quản trị Smart Parking" /></p>

*Dashboard Admin theo dõi hoạt động bãi xe.*

### 7.3 Database

Database sử dụng PostgreSQL trên Amazon RDS. Cơ sở dữ liệu lưu tài khoản, vị trí, booking, phiên gửi xe, gate event, phí và kết quả AI. Database stack đồng thời tạo Security Group giới hạn và secret do RDS quản lý. Ứng dụng kết nối qua endpoint/port đã cấu hình, còn credential được giữ trong Secrets Manager.

<p class="workshop-img"><img src="/images/5-Workshop/aws-04-rds-database.png" alt="Cơ sở dữ liệu PostgreSQL Smart Parking trên Amazon RDS" /></p>

*Cơ sở dữ liệu PostgreSQL của Smart Parking ở trạng thái Available.*

### 7.4 Payment

Dự án hiện chưa kết nối cổng thanh toán thật. Backend tính phí mô phỏng dựa trên thời lượng gửi xe và hiển thị kết quả trong màn hình doanh thu Admin. Cách làm này giữ workshop tập trung vào booking, cổng, dữ liệu và tích hợp AWS, đồng thời để thanh toán điện tử ở phần phát triển sau.

Thời gian tính phí được lấy từ lúc xe check-in đến khi Admin xác nhận check-out. Backend làm tròn thời lượng lên từng giờ, tối thiểu một giờ, rồi nhân với đơn giá mô phỏng. Kết quả được so sánh với khoản giữ chỗ: nếu phí cuối thấp hơn thì hệ thống ghi `refund_amount`; nếu xe đỗ lâu hơn làm phí cuối vượt khoản giữ chỗ thì phần chênh lệch được ghi vào `additional_amount` để Admin theo dõi khoản thu thêm.

Phiên bản hiện tại chưa tạo trạng thái phạt quá giờ riêng. Trường hợp xe ở lại lâu hơn thời gian dự kiến được phản ánh bằng thời lượng thực tế và khoản thu thêm khi check-out. Trạng thái booking `EXPIRED` chỉ áp dụng cho trường hợp người dùng không đến check-in sau thời gian chờ cho phép; khi đó QR hết hiệu lực và vị trí được trả về trạng thái trống.

<p class="workshop-img"><img src="/images/5-Workshop/07-admin-revenue.png" alt="Báo cáo phí mô phỏng Smart Parking" /></p>

*Tổng hợp phí gửi xe và doanh thu mô phỏng.*

## 8. Kế hoạch triển khai

| Giai đoạn | Nội dung chính |
|---|---|
| Giai đoạn 1 | Rà soát frontend, FastAPI, database migration, dữ liệu demo và biến môi trường. |
| Giai đoạn 2 | Chuẩn bị AWS CLI, dependency CDK, IAM profile được duyệt và Region triển khai. |
| Giai đoạn 3 | Triển khai Amazon RDS PostgreSQL, Security Group và secret do RDS quản lý. |
| Giai đoạn 4 | Triển khai S3, Cognito, Lambda AI, IAM policy và CloudWatch log group. |
| Giai đoạn 5 | Chạy migration, nạp dữ liệu demo, tạo object S3 kiểm thử và kết nối FastAPI với RDS. |
| Giai đoạn 6 | Khởi động frontend, kiểm tra đăng nhập User/Admin, booking, QR, cổng và sơ đồ vị trí. |
| Giai đoạn 7 | Kiểm thử AI, audit log, báo cáo phí, bằng chứng giám sát, chi phí và dọn dẹp. |

## 9. Kết quả kỳ vọng

Sau khi hoàn thành, hệ thống Smart Parking dự kiến đạt được:

- User đăng nhập, chọn thời gian và đặt được vị trí còn trống.
- Ứng dụng phát hành QR và quản lý đúng vòng đời booking.
- Admin theo dõi dashboard, xử lý sự kiện cổng và kiểm tra biển số không khớp.
- Sơ đồ bãi xe phản ánh trạng thái trống, đã đặt và đang có xe.
- Ảnh bằng chứng được lưu trên S3, dữ liệu nghiệp vụ được lưu trong PostgreSQL/RDS.
- Lambda và Rekognition cung cấp demo phát hiện trạng thái vị trí.
- Cognito group, IAM policy, CloudWatch log và CloudFormation stack có thể kiểm tra trên AWS Console.
- Người thực hiện có thể lặp lại quy trình deploy và giải thích các nhóm chi phí AWS chính.

## 10. Rủi ro và hướng xử lý

| Rủi ro | Hướng xử lý |
|---|---|
| Frontend không gọi được FastAPI | Kiểm tra API URL, CORS, backend health và cấu hình mạng. |
| Backend không kết nối được RDS | Kiểm tra endpoint, port, secret, migration và Security Group. |
| Không upload được ảnh lên S3 | Kiểm tra bucket, presigned URL, IAM policy, object key và thời hạn URL. |
| QR hoặc gate event bị xử lý lặp | Kiểm tra booking state và dùng `event_id` duy nhất với xử lý idempotent. |
| AI phân loại vị trí chưa chính xác | Cải thiện góc camera, chất lượng ảnh và điều chỉnh confidence threshold. |
| Chi phí AWS vượt dự kiến | Theo dõi Billing/Budgets và dừng/xóa tài nguyên workshop sau kiểm thử. |

## 11. Hướng phát triển

Các phiên bản sau có thể triển khai frontend và FastAPI hoàn toàn trên AWS, bổ sung CloudFront, Route 53 và pipeline CI/CD. Hệ thống cũng có thể phát triển OCR biển số tự động, thông báo thời gian thực, thanh toán điện tử, nhiều bãi xe, chính sách giá linh hoạt, phân tích occupancy nâng cao, Application Load Balancer và Auto Scaling.
