---
title: "Run and Demonstrate the System"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

This section connects the local application to the deployed AWS services and demonstrates the complete business workflow.

```text
User selects a slot → booking + PENDING QR
       ↓
Admin scans QR and stores entry image → enters plate → ACTIVE
       ↓
Slot camera → S3 → Lambda → Rekognition → observation result
       ↓
Admin scans the same QR at exit → new image + plate match → CLOSED
```

![Smart Parking operations overview](/images/5-Workshop/04-admin-overview.png)

*Figure 5.4-1: The operations center summarizes available slots, active sessions, alerts, and simulated revenue.*

## Steps

1. [Start the backend and frontend](5.4.1-prepare/)
2. [User reserves a slot and receives a QR](5.4.2-create-interface-enpoint/)
3. [Admin performs check-in and check-out](5.4.3-test-endpoint/)
4. [Monitor parking slots with AI](5.4.4-dns-simulation/)

{{% notice info %}}
The application supports both local and S3 storage. To collect AWS evidence in this workshop, start the backend with the AWS environment file and confirm `Storage Provider = s3` on the Admin settings page.
{{% /notice %}}
