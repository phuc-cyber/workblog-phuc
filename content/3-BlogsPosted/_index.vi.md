---
title: "Các blog đã biên dịch và chia sẻ"
date: 2026-07-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

# Các blog đã biên dịch và chia sẻ

Phần này ghi lại ba bài viết về AWS mà mình đã tìm hiểu, biên dịch, tóm lược và chia sẻ trong thời gian thực tập. Các chủ đề tập trung vào tự động hóa tác vụ theo lịch, tích hợp AI/ML vào ứng dụng và xây dựng chiến lược Disaster Recovery trên AWS.

## Các bài viết

### [Blog 1 - Amazon EventBridge Scheduler: Dịch vụ nhỏ nhưng hữu ích cho dự án AWS](3.1-Blog1/)
Bài viết giới thiệu cách **Amazon EventBridge Scheduler** tự động gọi Lambda và nhiều dịch vụ AWS theo lịch mà không cần duy trì EC2 hay một service chạy nền. Nội dung cũng trình bày các kiểu lập lịch, retry, Dead-letter Queue và trải nghiệm gọi Lambda mỗi 30 phút trong bài lab.

### [Blog 2 - Tích hợp AI vào ứng dụng với Amazon SageMaker và AWS AI Services](3.2-Blog2/)
Bài viết giới thiệu hai nhóm công cụ AI/ML trên AWS, quy trình chuẩn bị dữ liệu, huấn luyện và triển khai mô hình bằng Amazon SageMaker, cùng cách tích hợp nhanh các tính năng AI thông qua API và SDK.

### [Blog 3 - Xây dựng chiến lược Disaster Recovery và mạng phân tán trên AWS](3.3-Blog3/)
Bài viết trình bày vai trò của RTO/RPO, cơ chế block-level replication của AWS Elastic Disaster Recovery, mô hình kết nối tập trung bằng AWS Transit Gateway và các bước kiểm thử một kịch bản DR.

## Minh chứng bài đăng trên AWS Study Group VN

Các hình dưới đây ghi lại một số nội dung mình đã đăng trong cộng đồng AWS Study Group VN khi học AWS và triển khai dự án.

<p class="workshop-img"><img src="/images/3-BlogsTranslated/aws-vn-post-reliability-dr.png" alt="Bài đăng về AWS Reliability và Disaster Recovery"></p>

*Nội dung trao đổi về độ tin cậy trên AWS, chiến lược Disaster Recovery và dịch vụ AWS Elastic Disaster Recovery.*

<p class="workshop-img"><img src="/images/3-BlogsTranslated/aws-vn-post-sagemaker-ai.png" alt="Bài đăng về Amazon SageMaker và AWS AI Services"></p>

*Nội dung giới thiệu hướng đưa AI/ML vào ứng dụng bằng Amazon SageMaker cùng các dịch vụ AI trên AWS.*

<p class="workshop-img"><img src="/images/3-BlogsTranslated/aws-vn-post-eventbridge-scheduler.png" alt="Bài đăng về Amazon EventBridge Scheduler"></p>

*Nội dung minh họa việc dùng Amazon EventBridge Scheduler để tự động chạy những công việc theo lịch định sẵn.*
