# Serverless Visitor Counter on AWS

## Overview
This project demonstrates a simple serverless architecture on AWS.
A static website displays a visitor count that increments every time the page
is refreshed.

The objective of this project is to understand how core AWS serverless services
integrate with each other and to gain hands-on experience debugging real-world
issues such as region mismatches, IAM permissions, and API Gateway errors.

---

## Architecture

### High-Level Flow

+-------------+
| Browser |
+-------------+
|
v
+--------------------+
| S3 Static Website |
+--------------------+
|
v
+-------------------------+
| API Gateway (GET /count)|
+-------------------------+
|
v
+------------------+
| AWS Lambda |
| (Python Runtime) |
+------------------+
|
v
+------------------+
| DynamoDB |
| Visitors Table |
+------------------+


---

## Architecture Explanation

- **Frontend (Amazon S3)**  
  Hosts a static HTML page with JavaScript that calls a backend API.

- **API Layer (Amazon API Gateway)**  
  Exposes a REST endpoint that triggers backend logic.

- **Compute (AWS Lambda)**  
  Runs serverless Python code without managing servers.

- **Database (Amazon DynamoDB)**  
  Stores the visitor count using a simple key-value model.

---

## AWS Services Used

| Service | Purpose |
|------|--------|
| Amazon S3 | Static website hosting |
| API Gateway | Public REST API |
| AWS Lambda | Serverless backend logic |
| DynamoDB | Persistent visitor counter |
| IAM | Permissions management |
| CloudWatch | Logging and debugging |

---

## How the Application Works

1. The user opens the static website hosted on Amazon S3.
2. JavaScript sends a GET request to the API Gateway `/count` endpoint.
3. API Gateway invokes the Lambda function.
4. Lambda increments the counter stored in DynamoDB.
5. The updated count is returned and displayed on the webpage.

---

## Step-by-Step Setup (Recreate Anytime)

### Step 1: Create DynamoDB Table
- Table name: `Visitors`
- Partition key: `id` (String)

Initial item:

id = counter
count = 0


> DynamoDB table names are case-sensitive.

---

### Step 2: Create Lambda Function
- Runtime: Python 3.x
- Function name: `visitorCounter`

Responsibilities:
- Increment visitor count
- Fetch updated value
- Return JSON response
- Enable CORS

IAM Role must include:
AmazonDynamoDBFullAccess


---

### Step 3: Create API Gateway
- REST API
- Resource: `/count`
- Method: `GET`
- Integration: Lambda
- Enable CORS
- Deploy to stage: `prod`

Endpoint format:
https://<api-id>.execute-api.<region>.amazonaws.com/prod/count


---

### Step 4: Create S3 Static Website
- Create S3 bucket
- Disable block public access
- Upload `index.html`
- Enable static website hosting
- Add bucket policy for public read access

The frontend JavaScript fetches the API Gateway endpoint.

---

## Testing

- Open API Gateway `/count` URL in browser
- Refresh to verify counter increments
- Confirm updates in DynamoDB
- Review CloudWatch logs for Lambda execution

---

## Common Issues & Debugging

| Issue | Cause |
|-----|------|
| ResourceNotFoundException | Region mismatch or wrong table name |
| Missing Authentication Token | Incorrect API path |
| AccessDeniedException | Missing IAM permissions |
| CORS errors | CORS not enabled |

---

## Key Learnings

- AWS services are region-specific
- DynamoDB table names are case-sensitive
- IAM permissions are critical
- API Gateway error messages can be misleading
- CloudWatch logs are essential for debugging

---

## Improvements / Next Steps

- Infrastructure as Code using Terraform
- Add CloudFront for caching and security
- Secure API Gateway using IAM or API keys
- Add monitoring and alarms
- Implement CI/CD pipeline

---

## Cleanup & Cost Management

All AWS resources created for this project should be deleted after testing
to avoid unnecessary costs.

Planned enhancement:
terraform apply
terraform destroy


---

## Interview Talking Point

> I built a small AWS serverless project using S3, API Gateway, Lambda, and DynamoDB
> to strengthen my understanding of serverless architecture and service integration.
> I intentionally built it manually first and plan to automate it using Terraform.

