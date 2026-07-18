---
title: "Resource Cleanup and Decommissioning Process"
date: 2026-07-08
weight: 7
chapter: false
pre: " <b> 5.7 </b> "
---

#### Purpose

Thoroughly delete the test infrastructure after completing the system flow testing to optimize costs and prevent unwanted billing on AWS.

#### Detailed Execution Steps via AWS CloudShell

The operator opens the **AWS CloudShell** utility window and executes the following automated infrastructure cleanup phases sequentially:

**Step 1 — Disable RDS Deletion Protection and Delete Data in S3 Buckets**
Since the database and data buckets are configured with strict protection mechanisms to prevent accidental deletion, it is first necessary to dynamically query the RDS ID to remove the `deletion-protection` attribute, wait for the system to apply the available status, and then retrieve the bucket names from the Stack Outputs to recursively delete all data inside.

```bash
# Get DB Instance Identifier
DB_ID=$(aws rds describe-db-instances --region ap-southeast-1 \
  --query 'DBInstances[?contains(DBInstanceIdentifier,`vdcms`)].DBInstanceIdentifier' \
  --output text)

# Disable Deletion Protection
aws rds modify-db-instance \
  --db-instance-identifier "$DB_ID" \
  --no-deletion-protection \
  --apply-immediately \
  --region ap-southeast-1 > /dev/null

echo "Waiting for RDS to apply (~2 minutes)..."
aws rds wait db-instance-available \
  --db-instance-identifier "$DB_ID" --region ap-southeast-1

# Get bucket names from CloudFormation Outputs
FRONTEND_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`FrontendBucketName`].OutputValue' \
  --output text)

DATA_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name vdcms-prod --region ap-southeast-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`DataBucketName`].OutputValue' \
  --output text)

# Recursively delete all data inside the buckets
aws s3 rm "s3://$FRONTEND_BUCKET" --recursive
aws s3 rm "s3://$DATA_BUCKET" --recursive

echo "=== STEP 1 DONE ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7.png" style="max-width:100%; margin-bottom:16px;" />

**Step 2 — Delete CloudFormation Stack**
Execute the command to delete the entire infrastructure Stack `vdcms-prod`. This process will automatically decommission system resources including the EC2 server cluster, ALB, WAF firewall, database, and related IAM identities.

```bash
aws cloudformation delete-stack \
  --stack-name vdcms-prod --region ap-southeast-1

echo "Deleting stack, waiting 10–15 minutes..."
aws cloudformation wait stack-delete-complete \
  --stack-name vdcms-prod --region ap-southeast-1

echo "=== STACK DELETED ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7-4.png" style="max-width:100%; margin-bottom:16px;" />


**Step 3 — Delete Sensitive Configurations on Secrets Manager**
Use a loop to scan the entire ARN list of Secrets containing the project name string and perform a permanent deletion without a recovery waiting period to optimize costs.

```bash
for ARN in $(aws secretsmanager list-secrets --region ap-southeast-1 \
  --query 'SecretList[?contains(Name,`Secret`)].ARN' --output text); do
  aws secretsmanager delete-secret \
    --secret-id "$ARN" \
    --force-delete-without-recovery \
    --region ap-southeast-1
  echo "Deleted secret: $ARN"
done

echo "=== CLEANUP DONE ==="
```

<img src="/images/5-Workshop/5.7-Cleanup/5-7-3.png" style="max-width:100%; margin-bottom:16px;" />

---

#### Manual Verification and Cleanup on the AWS Console

After completing the automated command-line flow, the operator accesses the **AWS Management Console** to review, verify, and completely handle individual resources or automatically generated traces according to the guidance catalog below:

| Service | Operation Location | Required Action |
| :--- | :--- | :--- |
| **Remaining S3 buckets** | S3 → Buckets | Perform **Empty** data and click **Delete** sequentially for each remaining bucket. |
| **RDS Snapshots** | RDS → Snapshots | Select the 2 automated snapshots generated during the infrastructure deletion process → Select **Actions** → Click **Delete**. |

<img src="/images/5-Workshop/5.7-Cleanup/5-7-1.png" style="max-width:100%; margin-bottom:16px;" />

<img src="/images/5-Workshop/5.7-Cleanup/5-7-2.png" style="max-width:100%; margin-bottom:16px;" />