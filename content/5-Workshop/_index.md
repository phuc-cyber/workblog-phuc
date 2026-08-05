---
title: "Smart Parking Deployment Workshop on AWS"
date: 2026-07-30
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

This workshop presents the implementation and validation of the **Smart Parking System**. Users reserve parking slots and receive QR codes, gate operators confirm vehicle entry and exit using the QR code and plate images, and monitored parking slots use AI to detect whether a vehicle is present.

![Smart parking facility](/images/5-Workshop/smart-parking-hero.png)

{{% notice info %}}
The current environment uses a **services-only** deployment: Next.js and FastAPI run locally while application data and images use AWS services. No real payment gateway is connected; holds, refunds, and final fees are simulated in PostgreSQL.
{{% /notice %}}

## Project source

[Smart Parking source code on GitHub](https://github.com/phuc-cyber/carparking)

After completing the workshop, participants can:

- Explain the Next.js, FastAPI, RDS PostgreSQL, S3, Cognito, Lambda, Rekognition, and CloudWatch architecture.
- Run the frontend and backend locally while using real AWS data services.
- Demonstrate the complete reservation → QR → check-in → slot monitoring → check-out flow.
- Collect operational evidence in AWS Console without exposing secrets.

## Workshop content

1. [Solution overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Deploy AWS services](5.3-S3-vpc/)
4. [Run and demonstrate the system](5.4-S3-onprem/)
5. [Validate data and monitoring](5.5-Policy/)
6. [Cleanup and cost control](5.6-Cleanup/)
