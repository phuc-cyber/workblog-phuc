---
title: "Proposal"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Proposal for Deploying the Smart Parking Project on AWS

## 1. Project Title

**Deploying the Smart Parking Management Platform with AWS Services**

This project focuses on deploying a web-based parking platform that supports reservations, QR-based gate operations, parking-session tracking, and administrator monitoring. The system includes a Next.js and Fluent UI frontend, FastAPI business APIs, PostgreSQL data, S3 image evidence, Cognito groups, and an AI-assisted parking-slot workflow.

## 2. Background and Motivation

In a real-world parking system, the application should not only show a list of spaces but also keep booking state, verify entry and exit events, store image evidence, and provide an auditable operating history. Manual tickets and disconnected records make it difficult to know which slots are available, review a vehicle event, or trace an exception.

Deploying Smart Parking with AWS demonstrates how **Amazon RDS**, **Amazon S3**, **Amazon Cognito**, **AWS Lambda**, **Amazon Rekognition**, **IAM**, **AWS CDK**, **CloudFormation**, and **CloudWatch** can work together in a practical application. The frontend and FastAPI service can run locally or on a VPS while using the managed AWS resources.

## 3. Objectives

- Provide separate User and Admin workspaces with **Next.js + Fluent UI**.
- Allow a driver to choose a parking facility, arrival period, and available slot.
- Create booking QR codes and maintain `PENDING`, `ACTIVE`, `CLOSED`, `CANCELLED`, and `EXPIRED` states.
- Let Admins process QR-based check-in/check-out and record gate images and observed plates.
- Display parking spaces as `AVAILABLE`, `RESERVED`, or `OCCUPIED`.
- Store users, bookings, QR codes, sessions, fees, and audit events in **PostgreSQL/Amazon RDS**.
- Store gate and parking-camera images in **Amazon S3** through presigned URLs.
- Use **AWS Lambda + Amazon Rekognition** to determine whether a monitored slot contains a vehicle.
- Manage role-based access through **Amazon Cognito** and monitor AWS activity with **CloudWatch**.
- Define the infrastructure with **AWS CDK** and deploy it through CloudFormation.

## 4. Scope

The project scope focuses on a workshop and demonstration deployment. Users can register or sign in, inspect the parking map, select a period, reserve a slot, receive a QR code, and review booking history. Admins can monitor occupancy, operate the gate workflow, review plate mismatches, inspect audit records, and view simulated fee summaries.

The current version does not perform automatic license-plate OCR or real payment processing. An Admin reads the gate image and enters the observed plate; the backend normalizes and compares entry and exit values. API Gateway and CloudFront are not included in the current `services-only` deployment mode. They can be added later if the frontend and API are moved to a fully managed AWS hosting model.

## 5. Proposed Architecture

Users and Admins access the Next.js application through a browser. The frontend calls FastAPI for authorization and business workflows. FastAPI creates bookings and QR codes, updates gate and parking-session states, calculates simulated fees, and writes audit events to PostgreSQL/Amazon RDS. S3 stores gate and slot images through presigned URLs. The slot-occupancy Lambda invokes Rekognition on demand, while Cognito manages the `USER` and `ADMIN` groups and CloudWatch receives service logs.

![Car Parking deployment architecture on AWS](/images/5-Workshop/smart-parking-architecture-aws.svg)

*Smart Parking deployment workflow using local application services and managed AWS resources.*

## 6. AWS Services Used

| Service | Role in the project |
|---|---|
| **Amazon RDS PostgreSQL** | Stores users, parking slots, bookings, sessions, fees, and audit events. |
| **Amazon S3** | Stores plate, gate, QR, smoke-test, and parking-camera evidence with public access blocked. |
| **Amazon Cognito** | Manages the User Pool, App Client, and `USER`/`ADMIN` authorization groups. |
| **AWS Lambda** | Processes slot-camera images and runs supporting log-retention automation. |
| **Amazon Rekognition** | Performs on-demand label detection for the slot-occupancy demonstration. |
| **Security Group and IAM** | Restrict database and runtime access to the approved ports, resources, and actions. |
| **AWS CDK + CloudFormation** | Defines, reviews, and deploys the database and services stacks as infrastructure as code. |
| **Amazon CloudWatch** | Provides Lambda and RDS log groups for monitoring and troubleshooting. |

## 7. Component Design

### 7.1 Frontend

The frontend is built with Next.js and Fluent UI. It provides separate User and Admin workspaces, handles sign-in, presents the parking map, and calls FastAPI for reservations and operational actions. The current workshop can run the frontend locally or from a VPS while connecting to the AWS resources.

<p class="workshop-img"><img src="/images/5-Workshop/01-login.png" alt="Smart Parking sign-in page" /></p>

*Smart Parking authentication interface.*

<p class="workshop-img"><img src="/images/5-Workshop/02-user-booking.png" alt="User selecting a parking slot" /></p>

*User workflow for selecting a slot and creating a reservation.*

### 7.2 Backend

The backend is implemented with FastAPI. It validates permissions, creates bookings and QR codes, processes gate check-in/check-out, records audit events, and returns the data required by the User and Admin workspaces. The backend reads the database secret from the deployment environment instead of storing a password in source code.

<p class="workshop-img"><img src="/images/5-Workshop/04-admin-overview.png" alt="Smart Parking administrator dashboard" /></p>

*Administrator dashboard for monitoring parking operations.*

### 7.3 Database

The database uses PostgreSQL on Amazon RDS. It stores account references, parking slots, bookings, parking sessions, gate events, fees, and AI observations. The database stack also provisions a restricted Security Group and an RDS-managed secret. The application connects through the configured endpoint and port, while credentials remain in Secrets Manager.

<p class="workshop-img"><img src="/images/5-Workshop/aws-04-rds-database.png" alt="Smart Parking PostgreSQL database in Amazon RDS" /></p>

*Smart Parking PostgreSQL database in Available state.*

### 7.4 Payment

The current project does not connect to a real payment gateway. Instead, the backend calculates a simulated fee from parking duration and exposes the result to the Admin revenue view. This keeps the workshop focused on booking, gate, data, and AWS-service integration while leaving electronic payment as a future extension.

The billable duration starts at check-in and ends when an Admin confirms check-out. The backend rounds the elapsed time up to whole hours, with a minimum of one hour, and multiplies it by the simulated hourly rate. It then compares the final fee with the hold: a lower final fee produces `refund_amount`, while a longer stay that raises the fee above the hold produces `additional_amount` for the Admin to review.

The current version does not use a separate overtime-penalty state. A stay longer than expected is reflected by the actual elapsed duration and any additional charge calculated at check-out. The `EXPIRED` booking state has a different purpose: it applies when a user does not check in within the allowed grace period, after which the QR is invalidated and the slot becomes available again.

<p class="workshop-img"><img src="/images/5-Workshop/07-admin-revenue.png" alt="Smart Parking simulated fee report" /></p>

*Simulated parking fee and revenue summary.*

## 8. Implementation Plan

| Phase | Main tasks |
|---|---|
| Phase 1 | Review the frontend, FastAPI source, database migrations, demo data, and environment variables. |
| Phase 2 | Prepare AWS CLI, CDK dependencies, the approved IAM profile, and the deployment Region. |
| Phase 3 | Deploy Amazon RDS PostgreSQL, its Security Group, and the RDS-managed secret. |
| Phase 4 | Deploy S3, Cognito, the AI Lambda, IAM policy, and CloudWatch log groups. |
| Phase 5 | Run migrations, load demonstration data, upload S3 test objects, and connect FastAPI to RDS. |
| Phase 6 | Start the frontend and validate User/Admin sign-in, booking, QR, gate, and parking-map workflows. |
| Phase 7 | Test AI slot analysis, audit logs, fee reports, monitoring evidence, cost review, and cleanup. |

## 9. Expected Results

After completion, the Smart Parking system is expected to provide the following outcomes:

- Users can sign in, select a period, and reserve an available parking slot.
- The application issues QR codes and maintains the correct booking lifecycle.
- Admins can monitor the dashboard, process gate events, and review plate mismatches.
- The parking map reflects available, reserved, and occupied states.
- Image evidence is stored in S3 and operational records are stored in PostgreSQL/RDS.
- Lambda and Rekognition provide a working slot-occupancy demonstration.
- Cognito groups, IAM policies, CloudWatch logs, and CloudFormation stacks are verifiable in the AWS Console.
- The project owner can repeat the deployment and explain the main AWS cost drivers.

## 10. Risks and Mitigation

| Risk | Mitigation |
|---|---|
| Frontend cannot call the FastAPI service | Check the API URL, CORS configuration, backend health, and network settings. |
| Backend cannot connect to RDS | Check the endpoint, port, secret reference, migration status, and Security Group. |
| Images cannot be uploaded to S3 | Check the bucket, presigned URL, IAM policy, object key, and URL expiration. |
| QR or gate event is processed more than once | Validate booking state and use a unique `event_id` with idempotent processing. |
| AI slot classification is inaccurate | Improve the camera angle and image quality, then tune the confidence threshold. |
| AWS cost exceeds expectations | Monitor Billing/Budgets and stop or remove workshop resources after testing. |

## 11. Future Improvements

Future releases can host the frontend and FastAPI service fully on AWS, add CloudFront and Route 53, and introduce a CI/CD pipeline. The platform can also be extended with automatic plate OCR, real-time notifications, electronic payments, multiple parking facilities, flexible pricing rules, richer occupancy analytics, and an Application Load Balancer with Auto Scaling.
