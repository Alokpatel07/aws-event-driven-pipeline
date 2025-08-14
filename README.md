# PROJECT : Automating DynamoDB Entries via AWS Lambda on S3 Upload

## 📌 Project Overview
This project demonstrates how to use AWS S3 events to trigger a Lambda function that stores file metadata into DynamoDB.  
It uses **AWS Free Tier** services and is fully serverless.

## 🛠 AWS Services Used
- **Amazon S3** – File storage
- **AWS Lambda** – Event processing
- **Amazon DynamoDB** – NoSQL database
- **AWS IAM** – Permissions

## 📂 Architecture
1. File uploaded to S3 bucket.
2. S3 triggers a Lambda function.
3. Lambda writes file details to DynamoDB.

## ⚡ How It Works
1. Create S3 bucket.
2. Create DynamoDB table.
3. Create IAM role for Lambda.
4. Create Lambda function.
5. Add S3 trigger to Lambda.
6. Upload a file to S3 → Entry in DynamoDB.

## 📷 Screenshots
Screenshots are in `/screenshots` folder:
- 1_Bucket created Successfully
- 2_Role for DynamoDB Access
- 3_Lambda Function Created
- 4_DB Table created and is initially empty
- 5_Added S3 Trigger to Lambda
- 5_Uploaded Object on S3 bucket
- 6_Table is updated with an entry

## 🧹 Cleanup to Avoid Charges
- Delete the S3 bucket and objects.
- Delete the DynamoDB table.
- Delete the Lambda function and IAM role.