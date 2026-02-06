# Serverless Visitor Counter on AWS

## Overview
This project demonstrates a simple serverless architecture on AWS.
A static website displays a visitor count that increments every time the page is refreshed.

The goal of this project was to understand how multiple AWS services integrate
and to gain hands-on experience with serverless components.

---

## Architecture

User Browser  
→ S3 Static Website  
→ API Gateway (GET /count)  
→ AWS Lambda (Python)  
→ DynamoDB (Visitors table)

---

## AWS Services Used

- **Amazon S3**  
  Hosts the static HTML website.

- **Amazon API Gateway**  
  Exposes a public REST API endpoint to trigger backend logic.

- **AWS Lambda**  
  Runs serverless Python code to increment and retrieve the visitor count.

- **Amazon DynamoDB**  
  Stores the visitor count using a simple key-value NoSQL table.

- **AWS IAM**  
  Grants Lambda permission to access DynamoDB.

- **Amazon CloudWatch**  
  Used for logging and debugging Lambda execution.

---

## How It Works

1. The user opens the static website hosted on Amazon S3.
2. JavaScript in the page calls the API Gateway endpoint `/count`.
3. API Gateway invokes the Lambda function.
4. Lambda updates the visitor count stored in DynamoDB.
5. The updated count is returned and displayed on the webpage.

---

## Challenges & Learnings

- DynamoDB table names are case-sensitive.
- All AWS services must be in the same region to communicate.
- API Gateway requires the full resource path (e.g., `/count`) to avoid
  "Missing Authentication Token" errors.
- IAM roles are required for Lambda to access DynamoDB.
- CloudWatch logs are essential for debugging serverless applications.

---

## Improvements / Next Steps

- Convert infrastructure to Terraform for automated creation and deletion.
- Add CloudFront in front of S3 for caching and improved security.
- Secure API Gateway using IAM or API keys.
- Add monitoring and alarms using CloudWatch.

---

## Cleanup and Cost Awareness

All AWS resources created for this project should be deleted after testing
to avoid incurring costs.  
A future Terraform version will allow easy creation and teardown using
`terraform apply` and `terraform destroy`.



