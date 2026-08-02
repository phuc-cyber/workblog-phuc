---
title: "Worklog Week 11"
date: 2026-07-01
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

# Week 11: Deploying S3, Cognito, Lambda, and CloudWatch

**Period:** 20/07/2026 - 26/07/2026

## Objectives

- Complete the Car Parking services stack.
- Grant IAM permissions according to backend and Lambda requirements.
- Validate stack outputs and logs after deployment.

## Activities

| Date | Work completed |
| --- | --- |
| 20/07/2026 | Defined the S3 bucket and presigned-URL configuration. |
| 21/07/2026 | Created the Cognito User Pool, app client, and USER/ADMIN groups. |
| 22/07/2026 | Packaged the AI Lambda and granted S3 image-read access. |
| 23/07/2026 | Added the runtime IAM policy and CloudWatch Log Group. |
| 24/07/2026 | Deployed the services stack and validated CloudFormation outputs. |

## Outcomes

- S3, Cognito, Lambda, and CloudWatch are deployed through CDK.
- Components receive only the permissions required by their functions.
- Stack outputs can be applied to frontend and backend configuration.

## Product relation

The services stack completes the AWS infrastructure described in the Workshop.
