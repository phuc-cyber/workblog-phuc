---
title: "Blog 3 - Xây dựng chiến lược Disaster Recovery và mạng phân tán trên AWS"
date: 2026-07-29
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS Reliability & DR | Xây dựng chiến lược khắc phục sự cố và mạng phân tán trên AWS

## Giới thiệu

Những sự cố hạ tầng như mất điện tại trung tâm dữ liệu, đứt kết nối mạng hoặc thiên tai không thể được loại bỏ hoàn toàn. Đối với hệ thống quan trọng của doanh nghiệp, việc chuẩn bị trước một kế hoạch **Disaster Recovery (DR)** là điều cần thiết để giảm thời gian gián đoạn và hạn chế lượng dữ liệu có thể bị mất.

Hai chỉ số thường dùng để xác định mục tiêu phục hồi gồm:

- **RTO (Recovery Time Objective):** khoảng thời gian tối đa hệ thống có thể ngừng hoạt động trước khi được khôi phục.
- **RPO (Recovery Point Objective):** lượng dữ liệu tối đa có thể mất, được tính theo khoảng thời gian từ lần đồng bộ gần nhất.

Thông qua chuyên mục **Reliability** trên The First Cloud Journey, nhóm mình đã tìm hiểu cách vận hành **AWS Elastic Disaster Recovery (AWS DRS)** và cách tổ chức kết nối mạng tập trung giữa nhiều VPC bằng **AWS Transit Gateway**.

## 1. AWS Elastic Disaster Recovery là gì?

AWS DRS là dịch vụ khắc phục sự cố trên cloud, cho phép sao chép liên tục dữ liệu ở cấp block từ máy chủ vật lý, máy ảo hoặc workload trên cloud vào một staging area trên AWS. Nguồn có thể nằm tại hạ tầng on-premises hoặc một AWS Region khác với Region phục hồi.

Thay vì phải duy trì sẵn một cụm máy chủ dự phòng có cấu hình tương đương môi trường production, dữ liệu được đồng bộ vào vùng staging có chi phí thấp. Các EC2 Instance phục hồi chỉ được khởi chạy khi kiểm thử hoặc thực hiện failover.

## 2. Lợi ích của mô hình DR trên AWS

Nhóm mình đã tìm hiểu qua hai bài lab **Disaster Recovery with AWS Elastic Disaster Recovery** và **Centralized Network Management with AWS Transit Gateway**. Mô hình này mang lại ba lợi ích nổi bật:

### Tối ưu chi phí hạ tầng dự phòng

Trong trạng thái bình thường, staging area sử dụng tài nguyên vừa đủ cho hoạt động replication thay vì duy trì toàn bộ hệ thống dự phòng ở công suất cao. Cách tiếp cận này giúp giảm chi phí nhưng vẫn giữ dữ liệu sẵn sàng cho quá trình phục hồi.

### Hỗ trợ mục tiêu RPO và RTO thấp

Cơ chế block-level replication liên tục giúp dữ liệu tại môi trường phục hồi bám sát máy chủ nguồn. Khi cấu hình và kiểm thử đúng, giải pháp có thể hướng đến **RPO tính bằng giây** và **RTO tính bằng phút**, tùy thuộc vào workload và kiến trúc cụ thể.

### Quản lý kết nối mạng tập trung

AWS Transit Gateway có thể đóng vai trò hub kết nối nhiều VPC và mạng doanh nghiệp. So với việc tạo nhiều liên kết VPC Peering riêng lẻ, mô hình hub-and-spoke giúp định tuyến, mở rộng và quản trị mạng phân tán rõ ràng hơn.

## 3. Các bước thiết lập kịch bản DR

### Bước 1: Cài đặt AWS Replication Agent

Agent được cài trên máy chủ nguồn để đọc thay đổi ở cấp block, mã hóa dữ liệu trong quá trình truyền và gửi dữ liệu đến staging area trên AWS.

### Bước 2: Cấu hình Launch Settings

Đội ngũ xác định cấu hình sẽ được dùng khi phục hồi, bao gồm EC2 Instance Type, VPC, subnet, Security Group và những tùy chọn khởi chạy liên quan.

### Bước 3: Thực hiện DR Drill

Một bản diễn tập phục hồi nên được chạy định kỳ để xác nhận máy chủ có thể khởi động, ứng dụng hoạt động chính xác và kết nối mạng đáp ứng yêu cầu. Quá trình drill cần được tách khỏi production để không ảnh hưởng đến hệ thống đang phục vụ người dùng.

### Bước 4: Failover và Failback

Khi xảy ra sự cố thật, workload được chuyển sang môi trường phục hồi theo runbook đã chuẩn bị. Sau khi hệ thống nguồn ổn định, đội ngũ cần có kế hoạch failback, đồng bộ dữ liệu và chuyển hoạt động về môi trường chính một cách có kiểm soát.

## 4. Bài học rút ra

Một kế hoạch Disaster Recovery chỉ thực sự đáng tin cậy khi đã được kiểm thử thường xuyên. Việc có bản sao dữ liệu là chưa đủ; doanh nghiệp còn phải xác minh quy trình khởi chạy, kết nối mạng, phân quyền, giám sát và khả năng vận hành ứng dụng sau khi failover.

AWS DRS giúp đơn giản hóa phần replication và khởi chạy tài nguyên phục hồi, trong khi Transit Gateway hỗ trợ tổ chức kết nối giữa các môi trường. Tuy nhiên, đội ngũ vẫn cần xây dựng runbook rõ ràng, phân công trách nhiệm và đo lại RTO/RPO sau mỗi lần diễn tập.

## Hình ảnh bài đăng

<p class="workshop-img"><img src="/images/3-BlogsTranslated/aws-vn-post-reliability-dr.png" alt="Bài đăng về AWS Reliability và Disaster Recovery trên AWS Study Group VN"></p>

## Link bài đăng đã chia sẻ

[Bài đăng Facebook](https://www.facebook.com/share/p/1JVpYN6NkA/)

## Bài viết hướng dẫn

**Disaster Recovery with AWS Elastic Disaster Recovery | The First Cloud Journey**
