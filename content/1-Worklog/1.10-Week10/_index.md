---
title: "Worklog Week 10"
date: 2026-07-01
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

# Week 10: Deploying PostgreSQL to Amazon RDS with CDK

**Period:** 13/07/2026 - 19/07/2026

## Objectives

- Move the database from local PostgreSQL to Amazon RDS.
- Protect connection credentials in Secrets Manager.
- Define the database stack with AWS CDK.

## Activities

| Date | Work completed |
| --- | --- |
| 13/07/2026 | Reviewed migrations, demo data, and PostgreSQL version requirements. |
| 14/07/2026 | Defined RDS, its Security Group, and a secret in CDK. |
| 15/07/2026 | Ran CDK synth/diff and inspected the CloudFormation template. |
| 16/07/2026 | Deployed the database stack and retrieved the endpoint output. |
| 17/07/2026 | Ran migrations, loaded demo data, and tested FastAPI against RDS. |

## Outcomes

- Amazon RDS PostgreSQL runs behind a restricted Security Group.
- Database credentials are retained in Secrets Manager.
- The backend successfully reads and writes data in RDS.

## Product relation

RDS becomes the system of record for users, bookings, QR codes, sessions, fees, and audit logs.
