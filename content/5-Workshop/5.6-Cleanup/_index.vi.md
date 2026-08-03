---
title: "Dọn dẹp và kiểm soát chi phí"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Dừng ứng dụng local

Dừng frontend bằng `Ctrl + C`, sau đó:

```powershell
cd D:\Car-Parking
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml down
```

Lệnh này chỉ dừng container local. Nó **không** xóa dữ liệu RDS, ảnh S3 hoặc các tài nguyên AWS.

## Dọn dữ liệu demo

Nếu chỉ muốn làm sạch dữ liệu kiểm thử:

1. Xác nhận đúng môi trường Workshop.
2. Xóa object demo trong hai prefix `plates/` và `slot-observations/`, không xóa nhầm bucket khác.
3. Chỉ xóa record demo bằng migration/script đã được kiểm tra; không chạy câu lệnh xóa rộng trên database.
4. Giữ lại ảnh và log cần dùng làm bằng chứng trước khi xóa.

## Ngừng tài nguyên AWS

Trước khi destroy:

1. Chạy `cdk diff` và xác nhận đúng account/Region.
2. Chụp hoặc xuất các bằng chứng cần thiết.
3. Backup/snapshot RDS nếu dữ liệu còn giá trị.
4. Kiểm tra dependency giữa stack và ứng dụng.

```powershell
cd D:\Car-Parking\infra
npx cdk destroy -c deploymentMode=services-only --profile car-parking-deployer
```

RDS và S3 được cấu hình `RETAIN`; RDS còn có deletion protection. Vì vậy `cdk destroy` không đảm bảo xóa hoàn toàn các tài nguyên có dữ liệu.

{{% notice danger %}}
Chỉ xóa RDS/S3 vĩnh viễn khi đã được chủ tài khoản phê duyệt và đã xác nhận snapshot/backup. Sau khi xóa, dữ liệu có thể không khôi phục được.
{{% /notice %}}

## Kiểm tra sau cùng

- CloudFormation không còn stack không cần thiết.
- Không còn Lambda, log group hoặc bucket demo bị bỏ quên.
- RDS đã được giữ lại có chủ đích hoặc đã xóa theo quy trình được phê duyệt.
- Cost Explorer không tiếp tục tăng ngoài các tài nguyên được giữ.
- Budget Alert vẫn hoạt động cho tài khoản.
