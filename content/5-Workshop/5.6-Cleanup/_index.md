---
title: "Cleanup and Cost Control"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Cleanup and Cost Control

## Stop the local application

Stop the frontend with `Ctrl + C`, then run:

```powershell
cd D:\Car-Parking
docker compose --env-file .env.aws.local -f docker-compose.yml -f docker-compose.aws.yml down
```

This stops local containers only. It does **not** delete RDS data, S3 images, or AWS resources.

## Remove demo data

If only test data should be removed:

1. Confirm the correct workshop environment.
2. Delete demo objects only from `plates/` and `slot-observations/`; do not touch another bucket.
3. Remove database fixtures only with a reviewed migration/script; do not execute broad deletion queries.
4. Retain any images and logs required as report evidence.

## Retire AWS resources

Before destroying infrastructure:

1. Run `cdk diff` and verify the account and Region.
2. Capture or export required evidence.
3. Create an RDS backup/snapshot if the data has value.
4. Review dependencies between stacks and the application.

```powershell
cd D:\Car-Parking\infra
npx cdk destroy -c deploymentMode=services-only --profile car-parking-deployer
```

RDS and S3 use `RETAIN`; RDS also has deletion protection. Therefore, `cdk destroy` does not guarantee that data-bearing resources are fully removed.

{{% notice danger %}}
Permanently delete RDS/S3 only after account-owner approval and backup verification. Deleted data may not be recoverable.
{{% /notice %}}

## Final checks

- No unnecessary CloudFormation stacks remain.
- No unused Lambda, log group, or workshop bucket is forgotten.
- RDS is intentionally retained or removed through the approved procedure.
- Cost Explorer no longer increases beyond intentionally retained resources.
- The account Budget Alert remains enabled.
