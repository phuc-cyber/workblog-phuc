---
title: "Proposal"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Proposal for Building Car Parking on AWS

## 1. Project name

**Building a Smart Car Parking Management Platform with AWS Services**

The project proposes a web platform that lets drivers reserve parking spaces and enables administrators to monitor parking operations. It combines a Next.js interface, FastAPI services, PostgreSQL, booking-scoped QR codes, and AWS services for authentication, image storage, monitoring, and parking-slot analysis.

## 2. Motivation

Paper tickets and manual parking procedures make it difficult to identify available spaces, verify entry and exit events, or review operational history. Drivers also have no convenient way to select a space and arrival time before reaching the facility.

Car Parking addresses these limitations through role-based **User** and **Admin** applications. It also provides a practical environment for integrating **Amazon RDS**, **Amazon S3**, **Amazon Cognito**, **AWS Lambda**, **Amazon Rekognition**, **CloudWatch**, **AWS CDK**, and **CloudFormation**.

## 3. Project objectives

- Build separate User and Admin workspaces with **Next.js + Fluent UI**.
- Allow drivers to select a parking facility, arrival time, and available space.
- Create bookings and QR codes with `PENDING`, `ACTIVE`, `CLOSED`, `CANCELLED`, or `EXPIRED` states.
- Let Admins process check-in/check-out with QR codes, vehicle images, and manually observed plates.
- Display spaces as `AVAILABLE`, `RESERVED`, or `OCCUPIED`.
- Store users, bookings, QR codes, parking sessions, fees, and audit logs in **PostgreSQL/Amazon RDS**.
- Upload gate and parking-camera images to **Amazon S3** through presigned URLs.
- Use **Lambda + Rekognition** to determine whether a monitored space contains a vehicle.
- Manage authentication with **Amazon Cognito** and collect logs in **CloudWatch**.
- Define the infrastructure in **AWS CDK** and deploy it through CloudFormation.

## 4. Project scope

The current scope is a workshop and demonstration environment. Drivers can sign in, inspect the parking map, choose a period, reserve a space, receive a QR code, and review history. Administrators can monitor the dashboard, operate the gate, inspect current vehicles, review exceptions, read audit logs, and view simulated fee reports.

Rekognition is limited to detecting whether a monitored space contains a vehicle. The present version does not perform automatic license-plate OCR. An Admin reads the gate image and enters the plate, after which the backend normalizes and compares entry and exit values. Real payment processing is also outside the current scope.

## 5. Proposed architecture

Users and Admins access the Next.js application in a browser. The frontend calls FastAPI for authorization and business workflows. FastAPI reads and writes PostgreSQL or Amazon RDS data and creates presigned URLs for S3 image uploads. For parking-space monitoring, Lambda processes image events and invokes Rekognition to classify the space as occupied or empty. Cognito manages accounts and groups, while CloudWatch records AWS logs and metrics.

![Car Parking system architecture](/images/5-Workshop/smart-parking-architecture.svg)

<p style="text-align:center;"><em>Overall architecture of the Car Parking platform</em></p>

## 6. Components and technologies

| Component | Responsibility |
|---|---|
| **Next.js + Fluent UI** | Provides separate interfaces and workspaces for Users and Admins. |
| **FastAPI** | Exposes APIs, enforces authorization, and coordinates business workflows. |
| **PostgreSQL / Amazon RDS** | Stores accounts, bookings, QR codes, sessions, fees, and audit logs. |
| **Amazon S3** | Retains vehicle-gate images and monitored parking-space images. |
| **Amazon Cognito** | Manages a User Pool with `USER` and `ADMIN` groups. |
| **AWS Lambda + Rekognition** | Analyzes camera images to determine whether a space is occupied. |
| **CloudWatch** | Collects logs and metrics for Lambda and RDS. |
| **AWS CDK + CloudFormation** | Defines, reviews, and deploys infrastructure as code. |

## 7. Functional design

### 7.1 User interface

After signing in, a driver chooses a parking facility, arrival time, parking duration, and an available space on the map. Once the booking is created, the application provides a QR code for gate use and displays the active parking session.

<p class="workshop-img"><img src="/images/5-Workshop/01-login.png" alt="Car Parking sign-in page" /></p>
<p style="text-align:center;"><em>Car Parking authentication interface</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/02-user-booking.png" alt="Driver choosing a parking space and creating a booking" /></p>
<p style="text-align:center;"><em>Reservation interface for selecting a parking space</em></p>

### 7.2 Administrator interface

The Admin dashboard summarizes available spaces, vehicles currently parked, plates requiring review, location violations, and simulated revenue. Operational pages support QR processing, gate actions, parking-map inspection, and reporting.

<p class="workshop-img"><img src="/images/5-Workshop/04-admin-overview.png" alt="Administrator overview dashboard" /></p>
<p style="text-align:center;"><em>Real-time parking operations dashboard</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/05-admin-gate-control.png" alt="Car Parking gate-control screen" /></p>
<p style="text-align:center;"><em>Gate workflow for check-in and check-out</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/06-admin-parking-map.png" alt="Administrator parking-space map" /></p>
<p style="text-align:center;"><em>Map for monitoring the status of individual spaces</em></p>

<p class="workshop-img"><img src="/images/5-Workshop/07-admin-revenue.png" alt="Simulated revenue report" /></p>
<p style="text-align:center;"><em>Simulated fees and revenue summary</em></p>

### 7.3 Backend and data

FastAPI enforces access rules, creates bookings, issues QR codes, opens and closes parking sessions, and records audit events. Every gate operation carries a unique `event_id` to reduce duplicate processing. PostgreSQL stores workflow state and supports simulated fee calculations based on parking duration.

![Booking and parking-session lifecycle](/images/5-Workshop/smart-parking-flow.svg)

### 7.4 AWS services

The workshop infrastructure uses one database stack for RDS PostgreSQL, its Security Group, and Secrets Manager. A second services stack provisions S3, Cognito, the AI Lambda, runtime IAM policy, and CloudWatch Logs. The frontend and FastAPI can currently run locally or on a VPS while using these AWS resources.

![AWS services deployment model](/images/5-Workshop/smart-parking-deployment.svg)

## 8. Implementation plan

| Phase | Main work |
|---|---|
| Phase 1 | Review frontend, backend, database migrations, and demo data. |
| Phase 2 | Prepare AWS CLI, CDK, an IAM profile, and deployment environment variables. |
| Phase 3 | Deploy RDS PostgreSQL, its Security Group, and Secrets Manager. |
| Phase 4 | Provision S3, Cognito, the AI Lambda, IAM policy, and CloudWatch Logs. |
| Phase 5 | Run migrations, load demonstration data, and connect FastAPI to RDS. |
| Phase 6 | Start the frontend and validate User/Admin, booking, QR, and gate workflows. |
| Phase 7 | Test parking-camera analysis, audit logs, fee reports, and resource cleanup. |

## 9. Expected outcome

- Drivers can sign in, select a period, and reserve an available parking space.
- The system issues QR codes and maintains the correct booking lifecycle.
- Admins can monitor dashboards, process entry/exit events, and review plates.
- The parking map reflects available, reserved, and occupied states.
- Images are retained in S3 while operational data is stored in PostgreSQL/RDS.
- Lambda and Rekognition help detect parking-space occupancy from camera images.
- Audit logs, parking history, and simulated charges remain available for review.
- CDK and CloudFormation provide repeatable infrastructure deployment.

## 10. Risks and mitigations

| Risk | Mitigation |
|---|---|
| The frontend cannot call FastAPI | Verify the API URL, CORS configuration, backend health, and network settings. |
| FastAPI cannot connect to PostgreSQL/RDS | Check the connection string, secret, Security Group, and migration status. |
| A QR event is processed more than once | Use `event_id`, booking-state validation, and idempotent processing. |
| Images cannot be uploaded to S3 | Inspect the bucket, presigned URL, IAM policy, and URL expiration. |
| Rekognition produces an inaccurate result | Improve camera angle and image quality, then tune the confidence threshold. |
| AWS charges exceed expectations | Monitor Billing/Budgets and remove workshop resources after use. |

## 11. Future development

The frontend and FastAPI can later be hosted entirely on AWS instead of a local machine or VPS. Future releases may also add plate OCR, real-time notifications, electronic payments, support for multiple parking facilities, flexible fee rules, and an automated CI/CD pipeline.
