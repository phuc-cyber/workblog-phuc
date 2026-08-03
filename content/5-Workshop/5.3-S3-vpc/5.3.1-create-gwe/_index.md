---
title: "Prepare and Deploy with CDK"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## 1. Install dependencies

```powershell
cd D:\Car-Parking\infra
npm install
python -m pip install -r requirements.txt
```

## 2. Verify the AWS account and Region

```powershell
aws sts get-caller-identity --profile car-parking-deployer
aws configure get region --profile car-parking-deployer
```

The output must identify the approved deployment account and `ap-southeast-1`.

## 3. Synthesize and review changes

For the services stack:

```powershell
npm run synth -- -c deploymentMode=services-only
npm run diff -- -c deploymentMode=services-only --profile car-parking-deployer
```

For RDS, allow PostgreSQL port `5432` only from the approved current public IP:

```powershell
npm run synth -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32
npm run diff -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32 --profile car-parking-deployer
```

Replace `YOUR_PUBLIC_IP` with the approved address. Never use `0.0.0.0/0`.

## 4. Deploy

```powershell
npm run deploy -- -c deploymentMode=database-only -c allowedClientCidr=YOUR_PUBLIC_IP/32 --profile car-parking-deployer
npm run deploy -- -c deploymentMode=services-only --profile car-parking-deployer
```

CDK creates the database secret in Secrets Manager. Reference that secret from the backend environment; never copy the password into source code.

![Successful Smart Parking CloudFormation stacks](/images/5-Workshop/aws-01-cloudformation-stacks.png)

*Figure 5.3.1-1: CloudFormation confirms that the database and services stacks were deployed successfully in the Singapore Region.*

{{% notice warning %}}
Always review `cdk diff` before deployment. The workshop RDS instance enables encryption, short backup retention, deletion protection, and a `RETAIN` policy, so it will not automatically disappear after `cdk destroy`.
{{% /notice %}}
