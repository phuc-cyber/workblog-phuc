---
title: "Triển khai dịch vụ AWS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Triển khai dịch vụ AWS

Hạ tầng được khai báo bằng **AWS CDK v2** trong thư mục `infra/`. CDK tổng hợp mã Python thành CloudFormation template để việc triển khai có thể lặp lại và kiểm tra bằng `synth`/`diff`.

Phiên bản Workshop hiện dùng hai stack:

| Stack | Thành phần |
|---|---|
| `SmartParkingWorkshopDatabaseStack` | RDS PostgreSQL, Security Group, Secrets Manager |
| `SmartParkingWorkshopServicesStack` | S3, Cognito, Lambda AI, IAM runtime policy, CloudWatch Logs |

![Mô hình triển khai services-only](/images/5-Workshop/smart-parking-deployment.svg)

Trong mô hình này, frontend và FastAPI chưa được đưa lên Lambda/API Gateway/CloudFront. Chúng chạy trên máy local hoặc có thể chuyển lên VPS sau này, nhưng vẫn sử dụng cùng RDS, S3 và Lambda AI.

## Các bước

1. [Chuẩn bị và triển khai bằng CDK](5.3.1-create-gwe/)
2. [Kiểm tra CloudFormation và output](5.3.2-test-gwe/)

{{% notice info %}}
Repo cũng có stack `full` cho kiến trúc AWS hoàn chỉnh, nhưng đó không phải trạng thái đang dùng trong Workshop này. Không triển khai đồng thời backend trên cả Lambda và VPS/local vì sẽ tạo hai nguồn API và làm tăng chi phí.
{{% /notice %}}
