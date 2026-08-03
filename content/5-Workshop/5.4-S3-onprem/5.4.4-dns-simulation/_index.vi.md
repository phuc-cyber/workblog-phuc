---
title: "AI giám sát vị trí đỗ"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

Camera vị trí giúp so sánh trạng thái vật lý quan sát được với trạng thái mà hệ thống mong đợi.

## Luồng xử lý AWS

```text
Admin chọn vị trí có camera
        ↓
Chụp hoặc tải ảnh lên bằng presigned URL
        ↓
Amazon S3: slot-observations/
        ↓
FastAPI gọi Lambda đồng bộ
        ↓
Lambda gọi Rekognition DetectLabels
        ↓
occupied + confidence
        ↓
RDS slot_observations + cảnh báo nếu sai trạng thái
```

Lambda xem các label `Car`, `Vehicle`, `Truck`, `Motorcycle` hoặc `Bus` với ngưỡng confidence tối thiểu. Nếu có label phù hợp, vị trí được đánh dấu có xe.

## Thực hiện

1. Admin mở tab **Sơ đồ bãi xe**.
2. Chọn một vị trí demo đã bật `monitored`, ví dụ A1 hoặc A2.
3. Chọn chế độ **AI tự động**.
4. Mở webcam hoặc tải ảnh `.jpg`/`.png`.
5. Bấm **Phân tích và ghi nhận**.
6. Xem kết quả có xe/không có xe, confidence và lịch sử năm quan sát gần nhất.

![Sơ đồ bãi xe trong giao diện Admin](/images/5-Workshop/06-admin-parking-map.png)

*Hình 5.4.4-1: Admin theo dõi trạng thái từng vị trí và chọn các ô đã bật camera để thực hiện giám sát.*

## Phát hiện sai trạng thái

Backend so sánh:

```text
expected_occupied = (slot.status == "OCCUPIED")
violation_detected = (AI result != expected_occupied)
```

Ví dụ:

- Slot đang `OCCUPIED` nhưng AI không thấy xe → cảnh báo xe không ở đúng vị trí.
- Slot đang `AVAILABLE` nhưng AI thấy xe → cảnh báo xe đỗ không có phiên hợp lệ.

Kết quả được ghi vào `slot_observations`; vi phạm được ghi thêm vào audit log để Admin truy vết.

## Chế độ dự phòng

Nếu Lambda/Rekognition không sẵn sàng, Admin có thể chọn **Ngoại lệ thủ công**, ghi nhận có xe/không có xe và kèm ảnh. Ở môi trường local hoàn toàn, backend có thể dùng YOLOv8 thay cho Rekognition.

{{% notice info %}}
Rekognition xử lý theo yêu cầu và không lưu lịch sử riêng trong Rekognition Console. Bằng chứng đầy đủ gồm: ảnh trong S3, invocation/log Lambda và record trong RDS.
{{% /notice %}}
