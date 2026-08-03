---
title: "Start the Backend and Frontend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Terminal 1 — Backend connected to AWS

Ensure that Docker Desktop is running and the AWS CLI profile is valid.

```powershell
cd D:\Car-Parking
aws sts get-caller-identity --profile car-parking-deployer
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml up -d --build api
```

Apply migrations and verify the container:

```powershell
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml exec api alembic upgrade head
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml ps
```

If demo data is required, pass passwords through separate environment variables and never publish the real values:

```powershell
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml exec -e SEED_ADMIN_PASSWORD=YOUR_ADMIN_PASSWORD -e SEED_USER_PASSWORD=YOUR_USER_PASSWORD api python scripts/seed_workshop.py
```

AWS-local backend endpoints and services:

- API: `http://localhost:8001`
- Swagger: `http://localhost:8001/docs`
- RDS PostgreSQL instead of the local PostgreSQL container.
- S3 uploads through presigned URLs.
- AWS Lambda for parking-slot AI.

## Terminal 2 — Frontend

```powershell
cd D:\Car-Parking\frontend
npm install
npm run dev
```

Open:

- Sign-in page: `http://localhost:3000`
- User workspace: `http://localhost:3000/user`
- Admin workspace: `http://localhost:3000/admin`

## Quick validation

1. Swagger loads and the API responds.
2. The sign-in page displays its visual and form.
3. A successful login redirects to the workspace for that role.
4. Admin → Settings displays RDS/S3/Lambda integration status.

{{% notice warning %}}
Do not capture DevTools, logs, or environment files when they display connection strings, tokens, or presigned URLs. A presigned URL is time-limited but must still be treated as temporary sensitive data.
{{% /notice %}}
