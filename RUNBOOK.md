# RUNBOOK – S3 → Lambda → DynamoDB Upload Logger

This runbook explains how to **operate, monitor, and troubleshoot** the project that logs every S3 upload into DynamoDB.

---

## 1) What it Does (in plain English)
- Whenever you **upload a file to your S3 bucket**, AWS automatically **invoke a Lambda function**.
- That Lambda **captures details** (bucket name, object key, size, event time) and **stores a row** in a DynamoDB table.
- You can later **query the table** to see who uploaded what and when.

---

## 2) One-page Setup Recap (with screenshots)

1. **Create S3 bucket**  
[![S3 Bucket Created](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/1_Bucket%20created%20Successfully.png)]

2. **Create IAM role for Lambda**  
[![IAM Role for DynamoDB](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/2_Role%20for%20DynamoDB%20Access.png)]

4. **Create Lambda function & add code**  
[![Lambda Function Created](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/3_Lambda%20Function%20Created.png)]

5. **Create DynamoDB Table**  
[![DynamoDB Table Created](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/4_DB%20Table%20created%20and%20is%20initially%20empty.png)]

5. **Add S3 trigger to Lambda**  
[![S3 Trigger Added](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/5_Added%20S3%20Trigger%20to%20Lambda.png)]

6. **Upload a file to S3**  
[![File Uploaded to S3](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/6_Uploaded%20Object%20on%20S3%20bucket.png)]

7. **See row in DynamoDB**  
[![DynamoDB Updated](https://github.com/Alokpatel07/aws-event-driven-pipeline/blob/main/Screenshots/7_%20Table%20is%20updated%20with%20an%20entry.png)]


---

## 3) Daily Operation
- **To log**: Just upload files to the bucket. The system does the rest automatically.
- **To check results**: Open DynamoDB → your table → *Explore items*.
- **To review executions**: Open Lambda → *Monitor* → *CloudWatch logs*.

---

## 4) Monitoring Checklist
- **S3**: Event notifications are configured on your bucket.
- **Lambda**: No recent errors, invocations are increasing when you upload.
- **DynamoDB**: New items appear after each upload.
- **Permissions**: Lambda role still has the required access.

---

## 5) Common Issues (and quick fixes)
- **Nothing shows in DynamoDB**  
  - Likely the trigger wasn’t added to Lambda or the table name is wrong.  
  - Action: Check Lambda → *Configuration → Triggers* and confirm the table name in code (`ProjectTable`).

- **AccessDenied or ValidationException in logs**  
  - Lambda role missing permissions or wrong primary key schema.  
  - Action: Attach DynamoDB write + S3 read permissions; ensure table has a partition key that matches the item you put (example uses `eid` as string).

- **Duplicate/Dropped events**  
  - Multiple triggers or rare eventual consistency.  
  - Action: Ensure only **one** S3 event notification exists for the bucket. Lambda code is idempotent per `eid` (uuid), so duplicates are separate rows by design.

- **Function not triggering**  
  - Incorrect event type or bucket/region mismatch.  
  - Action: Verify the bucket belongs to the same region as the Lambda and that the trigger uses **ObjectCreated:Put**.

---

## 6) Maintenance (once a month is enough)
- Review CloudWatch errors for the Lambda.
- Trim or archive DynamoDB items you no longer need.
- Confirm IAM role policies follow least-privilege.
- (Optional) Enable DynamoDB on-demand backups if this becomes production-like.

---

## 7) Recovery Procedures
- **Trigger deleted by mistake** → Re-add S3 trigger in Lambda *Configuration → Triggers*.
- **Table corrupted or deleted** → Create a new table; update env var `TABLE_NAME` on the Lambda.
- **Bad deployment** → Revert Lambda to a previous version/zip or redeploy from `lambda/lambda_function.py`.

---

## 8) Clean-up to Avoid Charges
1. Empty and delete the S3 bucket.
2. Delete the DynamoDB table.
3. Delete the Lambda function.
4. Detach and delete the IAM role.

---

## 9) Useful CLI (optional)
```bash
aws s3 ls s3://<bucket-name>
aws dynamodb scan --table-name ProjectTable
```

**That’s it.** Simple, serverless, and free-tier friendly.
