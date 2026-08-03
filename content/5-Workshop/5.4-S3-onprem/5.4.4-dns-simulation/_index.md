---
title: "AI Parking-Slot Monitoring"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

Slot cameras compare the observed physical state with the state expected by the application.

## AWS processing flow

```text
Admin selects a monitored slot
        ↓
Capture or upload image through a presigned URL
        ↓
Amazon S3: slot-observations/
        ↓
FastAPI invokes Lambda synchronously
        ↓
Lambda calls Rekognition DetectLabels
        ↓
occupied + confidence
        ↓
RDS slot_observations + alert on state mismatch
```

The Lambda checks for `Car`, `Vehicle`, `Truck`, `Motorcycle`, or `Bus` labels above the minimum confidence threshold. A matching label marks the slot as occupied.

## Procedure

1. In Admin, open **Parking Map**.
2. Select a demo slot with `monitored` enabled, such as A1 or A2.
3. Select **Automatic AI** mode.
4. Open the webcam or choose a `.jpg`/`.png` image.
5. Select **Analyze and record**.
6. Review occupied/empty, confidence, and the five most recent observations.

![Parking map in the Admin interface](/images/5-Workshop/06-admin-parking-map.png)

*Figure 5.4.4-1: The Admin monitors each slot and selects camera-enabled slots for observation.*

## State mismatch detection

The backend compares:

```text
expected_occupied = (slot.status == "OCCUPIED")
violation_detected = (AI result != expected_occupied)
```

Examples:

- The slot is `OCCUPIED`, but AI sees no vehicle → the expected vehicle may be in the wrong slot.
- The slot is `AVAILABLE`, but AI sees a vehicle → a vehicle may be parked without a valid session.

The result is written to `slot_observations`; violations also create audit records for Admin review.

## Fallback mode

If Lambda/Rekognition is unavailable, the Admin can select **Manual exception**, record occupied/empty, and attach an image. In a completely local environment, the backend can use YOLOv8 instead of Rekognition.

{{% notice info %}}
Rekognition processes requests on demand and does not keep a separate history in the Rekognition Console. Complete evidence consists of the S3 image, Lambda invocation/log, and the RDS record.
{{% /notice %}}
