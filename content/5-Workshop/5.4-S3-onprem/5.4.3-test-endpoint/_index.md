---
title: "Admin Check-in and Check-out"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

# Admin Check-in and Check-out

## Entry check-in

1. Sign in with the `ADMIN` role.
2. Open **Gate Control** and select **Entry Gate**.
3. Scan the QR from the User screen or enter a demo token.
4. Open the webcam/select an image and capture the entry plate.
5. The Admin reads the plate in the image and enters it in the form.
6. Select **Process check-in**.

![Admin gate-control screen](/images/5-Workshop/05-admin-gate-control.png)

*Figure 5.4.3-1: The Admin scans a QR, captures a plate image, and processes check-in or check-out at the gate.*

The application:

- Confirms that the QR is valid and currently `PENDING`.
- Requests a presigned URL and uploads the image to the S3 `plates/` prefix.
- Normalizes the plate by uppercasing and removing separators.
- Creates a `parking_session` and `gate_event`.
- Changes the booking/QR to `ACTIVE` and the slot to `OCCUPIED`.
- Records the check-in time and an audit event.

{{% notice warning %}}
The gate image is evidence for the Admin to enter and verify the plate. The workshop does not call Rekognition OCR or an automatic OCR model for license plates.
{{% /notice %}}

## Exit check-out

1. Select **Exit Gate**.
2. Scan the same QR used at entry.
3. The system loads the `ACTIVE` session, entry image, assigned slot, and recorded plate.
4. Capture/upload a new exit image.
5. The Admin enters the observed exit plate and confirms the vehicle exit.

If the normalized plates match:

- The session and booking become `CLOSED`.
- The QR closes and the slot returns to `AVAILABLE`.
- The system calculates duration-based fees and records `final_fee`, `refund_amount`, or `additional_amount`.
- The exit image and gate event are retained.

### Fee calculation and longer-than-expected stays

The backend measures the interval from `entry_at` to check-out, rounds it up to whole hours, and always charges at least one hour. The final fee follows this formula:

```text
final fee = actual parked hours (rounded up) × simulated hourly rate
```

- If `final_fee` is lower than the hold, the difference is recorded as `refund_amount`.
- If a longer stay raises `final_fee` above the hold, the difference is recorded as `additional_amount`.
- These values are simulated; the system does not call a real payment gateway or collect money automatically.
- An `EXPIRED` booking means the user missed the check-in grace period; it is not an overtime state for a vehicle already inside the parking lot.

![Simulated revenue report](/images/5-Workshop/07-admin-revenue.png)

*Figure 5.4.3-2: The revenue page summarizes holds, final fees, refunds, and additional charges across parking sessions.*

If they do not match:

- The session becomes `REVIEW_REQUIRED`.
- Vehicle exit is not automatically confirmed.
- An Admin reviews both images, enters the corrected plate, and provides a reason.
- Every decision is written to the audit log.

## Test cases

| Scenario | Expected result |
|---|---|
| Scan a processed check-in QR again | No duplicate session |
| Expired or cancelled QR | Check-in rejected |
| Matching entry/exit plates | Session may close |
| Mismatched plates | `REVIEW_REQUIRED` |
| Admin denies checkout | Session remains active |
| Vehicle stays longer than expected | Charge the actual duration and record `additional_amount` when the fee exceeds the hold |
| `PENDING` booking misses the check-in grace period | Booking and QR become `EXPIRED`; slot returns to `AVAILABLE` |
| S3 upload fails | No partial business-state transition |

![QR and parking-session flow](/images/5-Workshop/smart-parking-flow.svg)
