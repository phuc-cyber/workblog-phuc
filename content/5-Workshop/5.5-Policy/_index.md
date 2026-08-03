---
title: "Validate Data and Monitoring"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

A successful demonstration must prove the full path from the UI to AWS instead of showing only a correct web page.

## 1. Amazon S3

Open the image bucket and inspect **Objects**:

- `plates/`: entry and exit gate images.
- `qrcodes/`: QR images generated for bookings.
- `smoke-tests/`: objects used to verify the deployment path.
- `slot-observations/`: parking-slot camera images, created after running the AI demonstration.

Keep **Block public access** enabled. The application uploads and reads images through time-limited presigned URLs.

![Smart Parking S3 object folders](/images/5-Workshop/aws-03-s3-objects.png)

*Figure 5.5-1: The deployed bucket contains the current plate, QR-code, and smoke-test evidence folders.*

## 2. Amazon RDS PostgreSQL

In RDS Console, validate:

- `Available` status.
- PostgreSQL engine.
- Database connections while the backend is active.
- No critical PostgreSQL log errors.

RDS Console does not display table rows directly. Connect with pgAdmin, DBeaver, or `psql` over SSL and execute read-only queries:

```sql
SELECT id, user_id, slot_id, start_time, end_time, status
FROM bookings ORDER BY id DESC LIMIT 20;

SELECT id, booking_id, entry_plate_number, exit_plate_number,
       entry_image_key, exit_image_key, status, match_result
FROM parking_sessions ORDER BY id DESC LIMIT 20;

SELECT id, event_type, booking_id, plate_number, image_key, decision, created_at
FROM gate_events ORDER BY id DESC LIMIT 20;

SELECT id, slot_id, image_key, occupied, confidence, observed_at
FROM slot_observations ORDER BY id DESC LIMIT 20;
```

Compare each RDS `image_key` with the corresponding S3 object key.

![Smart Parking PostgreSQL database in RDS](/images/5-Workshop/aws-04-rds-database.png)

*Figure 5.5-2: Amazon RDS reports the Smart Parking PostgreSQL instance as available.*

## 3. Lambda, Rekognition, and CloudWatch

1. Submit a slot-camera image in AI mode.
2. Open the slot-occupancy Lambda and select **Monitor**.
3. Confirm that `Invocations` increased and `Errors = 0` for a successful request.
4. Open the newest CloudWatch log stream.
5. Compare the result with the new `slot_observations` record.

Rekognition `DetectLabels` is an on-demand request, so the Rekognition Console does not provide a separate image history.

![Smart Parking Lambda functions](/images/5-Workshop/aws-05-lambda-functions.png)

*Figure 5.5-3: Lambda functions deployed for AI occupancy processing and CloudWatch log retention.*

![CloudWatch log groups for Smart Parking](/images/5-Workshop/aws-07-cloudwatch-log-groups.png)

*Figure 5.5-4: CloudWatch receives logs from the AI Lambda, log-retention function, and RDS PostgreSQL.*

## 4. Cognito

Inspect the User Pool, App Client, and `USER`/`ADMIN` groups. If Cognito authentication is enabled in the backend, a newly registered account should appear in Users and its assigned group. In local-auth mode, the account appears only in RDS; clearly state this configuration difference in the report.

![Cognito USER and ADMIN groups](/images/5-Workshop/aws-06-cognito-groups.png)

*Figure 5.5-5: The Cognito User Pool contains the `USER` and `ADMIN` authorization groups.*

## 5. Evidence checklist

- [ ] Both CloudFormation stacks deployed successfully.
- [ ] RDS is `Available` and contains records from the demo.
- [ ] S3 contains the expected evidence folders, including `plates/`, `qrcodes/`, and `slot-observations/` after the AI demonstration.
- [ ] Booking moved `PENDING → ACTIVE → CLOSED`.
- [ ] Slot moved `AVAILABLE → RESERVED → OCCUPIED → AVAILABLE`.
- [ ] Lambda received a new invocation and CloudWatch contains its log.
- [ ] RDS contains the corresponding slot-camera result.
- [ ] A plate mismatch created `REVIEW_REQUIRED` and an audit event.
- [ ] Screenshots contain no secret, token, or password.
- [ ] API Gateway/CloudFront are correctly described as absent in `services-only` mode.

## 6. Cost validation

Open **Billing and Cost Management → Cost Explorer**, group by Service, and review RDS, S3, Lambda, Rekognition, CloudWatch, and Secrets Manager. RDS is the most important continuously running workshop resource; a Budget sends alerts but does not stop services.
