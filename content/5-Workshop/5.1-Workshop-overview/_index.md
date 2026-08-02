---
title: "Solution Overview"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Solution Overview

## Problem

Traditional parking facilities depend heavily on paper tickets and manual operation. Operators cannot easily see real-time slot availability, trace gate history, or verify that the correct vehicle leaves the facility. This project addresses those problems with role-based **User/Admin** web applications, booking-scoped QR codes, and AWS data and AI services.

## Core capabilities

### User

- Register or sign in.
- Select a parking lot, time range, and available slot.
- Create a booking and receive a `PENDING` QR code.
- View booking state, simulated hold amount, and parking history.

### Administrator

- View dashboards, vehicles in the facility, and slot states.
- Scan QR codes, capture vehicle images, and enter the observed plate for check-in/check-out.
- Monitor selected parking slots using camera images and AI.
- Review plate mismatches, audit logs, and simulated fee reports.

## Current architecture

![Smart Parking architecture](/images/5-Workshop/smart-parking-architecture.svg)

| Component | Responsibility |
|---|---|
| Next.js + Fluent UI | User and Admin interfaces |
| FastAPI | APIs, authorization, and business workflows |
| Amazon RDS PostgreSQL | System of record for users, bookings, QR codes, sessions, fees, and logs |
| Amazon S3 | Stores gate and slot-camera images through presigned URLs |
| Amazon Cognito | User Pool with `USER` and `ADMIN` groups, ready for AWS authentication mode |
| AWS Lambda + Rekognition | Detects occupied/empty monitored parking slots |
| CloudWatch | Lambda and RDS logs and metrics |
| AWS CDK + CloudFormation | Infrastructure definition and deployment |

{{% notice warning %}}
Rekognition is used only for **parking-slot monitoring**. The current implementation does not run automatic plate OCR. An Admin reads the gate image and enters the plate; the backend normalizes and compares entry and exit values.
{{% /notice %}}

## Business lifecycle

![QR and parking-session flow](/images/5-Workshop/smart-parking-flow.svg)

```text
Booking/QR: PENDING → ACTIVE → CLOSED
                    ↘ CANCELLED
                    ↘ EXPIRED

Slot: AVAILABLE → RESERVED → OCCUPIED → AVAILABLE
```

Each gate operation uses a unique `event_id` to reduce duplicate processing. Separate entry and exit images are retained, state transitions are recorded in the audit log, and the final simulated fee is calculated from the parking duration.
