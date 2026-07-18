---
title: "Infrastructure Deployment Process via Command Line"
date: 2026-07-08
weight: 4
chapter: false
pre: " <b> 5.4 </b> "
---

#### Objective

Automate the process of provisioning servers, configuring networks, and assigning system permissions by executing code, thereby minimizing errors caused by manual operations.

#### Detailed Implementation Steps

1. From the AWS navigation bar, open the **AWS CloudShell** utility window.
2. Remove the old junk directory (if any) and download the latest source code version from the Production branch on Github using the following commands:

```bash
rm -rf /tmp/vdcms
git clone --branch dev [https://github.com/BuiThanhPhuoc/Voice-Driven-Construction-Management-System.git](https://github.com/BuiThanhPhuoc/Voice-Driven-Construction-Management-System.git) /tmp/vdcms
```
<img src="/images/5-Workshop/5.4-CloudFormation/5-4.png" style="max-width:100%; margin-bottom:16px;" />

3. Launch the automated infrastructure deployment process by invoking the `aws cloudformation deploy` tool. The input parameters include: Stack name (`vdcms-prod`), template configuration file (`/tmp/vdcms/infra/cloudformation/vdcms-production.yml`), identity capability acknowledgment (`CAPABILITY_IAM`), and the input parameter for the primary notification email address (`admin.vdcms@gmail.com`):

```bash
aws cloudformation deploy \
  --region ap-southeast-1 \
  --stack-name vdcms-prod \
  --template-file /tmp/vdcms/infra/cloudformation/vdcms-production.yml \
  --capabilities CAPABILITY_IAM \
  --parameter-overrides \
    RepositoryBranch="dev" \
    RepositoryURL="[https://github.com/BuiThanhPhuoc/Voice-Driven-Construction-Management-System.git](https://github.com/BuiThanhPhuoc/Voice-Driven-Construction-Management-System.git)" \
    BootstrapAdminEmail="admin.vdcms@gmail.com" \
    SesFromEmail="admin.vdcms@gmail.com" \
  --no-fail-on-empty-changeset
```
<img src="/images/5-Workshop/5.4-CloudFormation/5-4-1.png" style="max-width:100%; margin-bottom:16px;" />

4. The system will automatically initialize the Stack. The operator can directly check the visual progress in the form of a bar chart (Timeline view) in the **Events** section of the CloudFormation Console to monitor the creation process of VPC network components, Subnets, Databases, and corresponding IAM permissions.

<img src="/images/5-Workshop/5.4-CloudFormation/5-4-2.png" style="max-width:100%; margin-bottom:16px;" />

5. After receiving the command line notification `Successfully created/updated stack - vdcms-prod`, proceed to automatically retrieve the Stack's output information (**Outputs**) and assign it to environment variables to facilitate the source code deployment:

```bash
# Retrieve outputs information from stack
BUCKET=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`FrontendBucketName`].OutputValue' \
  --output text)

DIST=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`CloudFrontDistributionId`].OutputValue' \
  --output text)

APP_URL=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`ApplicationUrl`].OutputValue' \
  --output text)

echo "App URL: $APP_URL"
echo "S3 Bucket: $BUCKET"
echo "CloudFront ID: $DIST"
```

6. Navigate to the directory containing the Frontend source code (`cd /tmp/vdcms/FE`), set the configuration environment variables pointing to the API address and Socket port, run the `npm ci` command, bundle the resources, and upload everything to the static S3 Bucket. Finally, clear the old cache data on the CloudFront Distribution:

```bash
# Build and upload Frontend
cd /tmp/vdcms/FE
npm ci
VITE_API_URL=/api VITE_SOCKET_URL="" npm run build
aws s3 sync ./dist "s3://$BUCKET" --region ap-southeast-1 --delete
aws cloudfront create-invalidation --distribution-id $DIST --paths '/*'

echo "=== FE DEPLOY DONE ==="
```
<img src="/images/5-Workshop/5.4-CloudFormation/5-4-3.png" style="max-width:100%; margin-bottom:16px;" />

7. Check the API responsiveness through CloudFront by sending a sample HTTP POST request:

```bash
# Check API via CloudFront
curl -s -X POST "$APP_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"username":"vdcmsadmin","password":"wrongpass"}'
  
# Expected: {"success":false,"message":"Tên đăng nhập hoặc mật khẩu không chính xác!"}
```

8. **PHASE 7 — Retrieve Admin credentials:** Query the ARN of the Secret managing the Admin account and call the AWS Secrets Manager service to retrieve the root login information of the system:

```bash
ADMIN_SECRET_ARN=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`BootstrapAdminSecretArn`].OutputValue' \
  --output text)

aws secretsmanager get-secret-value \
  --secret-id "$ADMIN_SECRET_ARN" \
  --region ap-southeast-1 \
  --query SecretString --output text
  
# Output: {"username":"vdcmsadmin","email":"...","password":"..."}
```
<img src="/images/5-Workshop/5.4-CloudFormation/5-4-4.png" style="max-width:100%; margin-bottom:16px;" />