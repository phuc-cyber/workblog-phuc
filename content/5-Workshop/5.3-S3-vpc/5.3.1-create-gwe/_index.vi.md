---
title: "Chuẩn bị và triển khai bằng CDK"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## 1. Cài dependency

```powershell
cd D:\Car-Parking\infra
npm install
python -m pip install -r requirements.txt
```

## 2. Xác minh AWS account và Region

```powershell
aws sts get-caller-identity --profile car-parking-deployer
aws configure get region --profile car-parking-deployer
```

Kết quả phải đúng account được phép triển khai và Region `ap-southeast-1`.

## 3. Tổng hợp và xem thay đổi

Đối với stack dịch vụ:

```powershell
npm run synth -- -c deploymentMode=services-only
npm run diff -- -c deploymentMode=services-only --profile car-parking-deployer
```

Đối với RDS, chỉ cho phép IP công khai hiện tại kết nối cổng `5432`:

```powershell
npm run synth -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32
npm run diff -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32 --profile car-parking-deployer
```

Thay `YOUR_PUBLIC_IP` bằng IP được phê duyệt. Không dùng `0.0.0.0/0`.

## 4. Triển khai

```powershell
npm run deploy -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32 --profile car-parking-deployer
npm run deploy -- -c deploymentMode=services-only --profile car-parking-deployer
```

CDK tạo secret cho database trong Secrets Manager. Chỉ tham chiếu secret hoặc sử dụng nó trong môi trường backend; không sao chép password vào source.

![Hai stack CloudFormation của Smart Parking đã triển khai thành công](/images/5-Workshop/aws-01-cloudformation-stacks.png)

*Hình 5.3.1-1: CloudFormation xác nhận Database Stack và Services Stack đã triển khai thành công tại Region Singapore.*

{{% notice warning %}}
Luôn đọc `cdk diff` trước khi deploy. RDS trong Workshop bật mã hóa, backup ngắn hạn, deletion protection và chính sách `RETAIN`, vì vậy tài nguyên không tự biến mất khi chạy `cdk destroy`.
{{% /notice %}}
