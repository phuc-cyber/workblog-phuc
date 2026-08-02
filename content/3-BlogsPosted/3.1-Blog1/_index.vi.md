---
title: "Blog 1 - Amazon EventBridge Scheduler: Dịch vụ nhỏ nhưng hữu ích cho dự án AWS"
date: 2026-07-29
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Amazon EventBridge Scheduler | Dịch vụ nhỏ nhưng rất hữu ích khi làm dự án trên AWS

## Giới thiệu

Xin chào mọi người, mình là một thành viên của nhóm **Flux**. Trong quá trình thực hiện đồ án và thử nghiệm các dịch vụ AWS, nhóm mình phát sinh nhiều công việc cần chạy theo lịch, chẳng hạn gọi Lambda định kỳ, gửi thông báo, đồng bộ dữ liệu hoặc tự động xử lý một tác vụ vào thời điểm cố định mỗi ngày.

Ban đầu, nhóm dự định sử dụng Cron Job trên EC2 hoặc tự xây dựng một service chạy nền. Tuy nhiên, cách làm này đòi hỏi phải duy trì máy chủ, theo dõi tiến trình và tự xử lý khi tác vụ gặp lỗi. Sau khi tham khảo tài liệu AWS, nhóm mình biết đến **Amazon EventBridge Scheduler** — một dịch vụ giúp giải quyết nhu cầu lập lịch đơn giản hơn đáng kể.

Sau thời gian học và triển khai thử trong bài lab, mình nhận thấy đây là một dịch vụ nhỏ gọn nhưng rất đáng để sử dụng trong các dự án AWS.

## Vì sao EventBridge Scheduler tiện lợi?

### Không cần duy trì máy chủ

Ưu điểm mình đánh giá cao nhất là không phải tạo thêm EC2 chỉ để chạy Cron Job. Người dùng chỉ cần khai báo lịch và target; AWS sẽ tự kích hoạt tác vụ vào đúng thời điểm đã cấu hình. Nhờ đó, nhóm tiết kiệm được thời gian thiết lập và không phải quản lý một máy chủ chạy nền.

### Tích hợp trực tiếp với nhiều dịch vụ AWS

EventBridge Scheduler có thể gửi yêu cầu đến nhiều dịch vụ, tiêu biểu như:

- **AWS Lambda**
- **Amazon ECS**
- **AWS Step Functions**
- **Amazon SNS**
- **Amazon SQS**
- **Amazon EventBridge Event Bus**

Đối với bài thực hành, bản demo hoặc dự án nhỏ, chỉ cần một vài bước cấu hình là đã có thể hình thành một quy trình tự động tương đối hoàn chỉnh.

### Hỗ trợ nhiều hình thức lập lịch

Dịch vụ cung cấp các lựa chọn linh hoạt theo từng nhu cầu:

- Chạy một lần tại thời điểm xác định (**One-time Schedule**).
- Chạy lặp lại theo biểu thức cron (**Cron Expression**).
- Chạy định kỳ theo một khoảng thời gian (**Rate Expression**).

## Những tính năng hữu ích

Trong quá trình tìm hiểu, mình nhận thấy EventBridge Scheduler còn tích hợp sẵn nhiều cơ chế thường phải tự xây dựng nếu sử dụng Cron Job truyền thống:

- **Retry Policy:** tự động thử lại khi lần thực thi đầu tiên thất bại.
- **Dead-letter Queue:** lưu những request không thể xử lý để kiểm tra và khắc phục sau.
- **Flexible Time Window:** phân bổ thời điểm thực thi trong một khoảng cho phép, giúp hạn chế tình trạng nhiều schedule cùng chạy đồng thời.
- **Quản lý tập trung:** theo dõi và cấu hình các schedule trực tiếp trên AWS Management Console.

Các tính năng này đặc biệt phù hợp với dự án học tập vì nhóm không cần phát triển thêm hệ thống retry, lưu lỗi hay quản lý tiến trình nền.

## Trải nghiệm thực tế

Trong một bài lab, nhóm mình sử dụng EventBridge Scheduler để gọi Lambda cập nhật dữ liệu sau mỗi **30 phút**. Toàn bộ quá trình cấu hình chỉ mất vài phút và không cần tạo thêm EC2 để vận hành Cron Job.

Nếu triển khai theo cách truyền thống, luồng xử lý thường gồm nhiều thành phần:

`EC2 hoặc server chạy nền → Cron Job/service → tác vụ cần thực hiện → cơ chế giám sát và thử lại`

Với EventBridge Scheduler, luồng được rút gọn thành:

`Schedule → dịch vụ AWS đích`

Qua lần thử nghiệm này, mình thấy dịch vụ giúp giảm đáng kể công sức vận hành nhưng vẫn đáp ứng tốt nhu cầu tự động hóa theo lịch.

## Hình ảnh bài đăng

<p class="workshop-img"><img src="/images/3-BlogsTranslated/aws-vn-post-eventbridge-scheduler.png" alt="Bài đăng Amazon EventBridge Scheduler trên AWS Study Group VN"></p>

## Link bài đăng đã chia sẻ

[Bài đăng Facebook](https://www.facebook.com/share/p/17ywuoYwuE/)
