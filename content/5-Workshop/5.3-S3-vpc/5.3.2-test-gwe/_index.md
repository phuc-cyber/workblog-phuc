---
title: "Validate CloudFormation and Outputs"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

# Validate CloudFormation and Outputs

## CloudFormation

1. Open AWS Console and select **Asia Pacific (Singapore)**.
2. Go to **CloudFormation → Stacks**.
3. Open `SmartParkingWorkshopDatabaseStack`.
4. Confirm a successful status and inspect **Resources**, **Events**, and **Outputs**.
5. Repeat for `SmartParkingWorkshopServicesStack`.

The database stack should contain:

- An RDS PostgreSQL instance.
- A Security Group restricted to the configured CIDR.
- An RDS-managed secret.

The services stack should contain:

- An image S3 bucket with public access blocked and HTTPS enforced.
- A Cognito User Pool, App Client, and `USER`/`ADMIN` groups.
- A Python 3.12 Lambda for slot-camera analysis.
- A runtime IAM policy restricted to the required bucket, Lambda, and User Pool.

## Record outputs safely

The backend may use these non-secret configuration values:

- AWS Region.
- S3 bucket name.
- Cognito User Pool ID and App Client ID.
- Slot-occupancy Lambda name.
- RDS endpoint, port, database name, and the **secret ARN**.

{{% notice warning %}}
Do not include the password stored inside the secret in the report or screenshots. It is sufficient to demonstrate that the secret exists and that the application references the correct ARN.
{{% /notice %}}

## Expected result

- Both stacks have a successful status.
- RDS reports `Available`.
- S3, Cognito, and Lambda are visible in the selected Region.
- API Gateway and CloudFront are absent in `services-only` mode by design; this is not a deployment failure.
