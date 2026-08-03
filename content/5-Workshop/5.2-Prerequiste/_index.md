---
title: "Prerequisites"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Local tools

- Windows 10/11.
- Git.
- Docker Desktop and Docker Compose.
- Node.js and npm.
- Python 3.10 or later for the backend and AWS CDK.
- AWS CLI and AWS CDK CLI.
- A webcam or `.jpg`/`.png` images for gate and slot-camera simulation.

Verify the tools:

```powershell
docker --version
node --version
npm --version
python --version
aws --version
```

## AWS account

- Use the `ap-southeast-1` (Singapore) Region.
- Enable MFA for administrative access.
- Use a dedicated IAM user/role for deployment instead of the root account.
- Create a Budget Alert before provisioning RDS.
- The project uses the local profile `car-parking-deployer`.

```powershell
aws sts get-caller-identity --profile car-parking-deployer
```

## Source and configuration

```text
D:\Car-Parking
├── backend/       FastAPI, SQLAlchemy, Alembic
├── frontend/      Next.js, Fluent UI
├── infra/         AWS CDK
├── docker-compose.yml
└── docker-compose.aws.yml
```

The server requires environment variables for RDS, S3, the AWS Region, Cognito, and the AI Lambda. Keep them in a local environment file or an approved secret store.

{{% notice warning %}}
Never put the database password, Access Key, Secret Key, or `.env.aws.local` contents in Git, frontend code, or the report. The frontend should receive only its API URL and required public configuration.
{{% /notice %}}

## Demo scope

- One entry gate and one exit gate.
- One demo parking lot with a small number of slots; only one or two require AI monitoring.
- Payments are simulated; no VNPay, MoMo, Stripe, or banking API is called.
- Frontend/backend run locally while AWS provides the database, storage, identity resources, and AI service.
