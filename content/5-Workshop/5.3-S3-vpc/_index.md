---
title: "Deploy AWS Services"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Deploy AWS Services

Infrastructure is defined with **AWS CDK v2** in `infra/`. CDK synthesizes the Python definitions into CloudFormation templates, making deployments repeatable and reviewable through `synth` and `diff`.

The current workshop uses two stacks:

| Stack | Components |
|---|---|
| `SmartParkingWorkshopDatabaseStack` | RDS PostgreSQL, Security Group, Secrets Manager |
| `SmartParkingWorkshopServicesStack` | S3, Cognito, AI Lambda, runtime IAM policy, CloudWatch Logs |

![Services-only deployment on AWS](/images/5-Workshop/smart-parking-architecture-aws.svg)

In this mode, the frontend and FastAPI API are not deployed to Lambda/API Gateway/CloudFront. They run locally or can later move to a VPS while continuing to use the same RDS, S3, and AI Lambda resources.

## Steps

1. [Prepare and deploy with CDK](5.3.1-create-gwe/)
2. [Validate CloudFormation and outputs](5.3.2-test-gwe/)

{{% notice info %}}
The repository also contains a `full` AWS stack, but it is not the environment used in this workshop. Do not run the same backend on both Lambda and a VPS/local host at the same time; this creates multiple API authorities and unnecessary cost.
{{% /notice %}}
