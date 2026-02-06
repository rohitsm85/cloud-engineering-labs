# Serverless Visitor Counter (AWS)

A simple serverless application that tracks and displays the number of visitors using
AWS Lambda, API Gateway, and DynamoDB.

This project demonstrates core AWS serverless concepts and is designed as a
portfolio-ready example for cloud / DevOps interviews.

------------------------------------------------------------
ARCHITECTURE OVERVIEW
------------------------------------------------------------

User / Browser
     |
     |  HTTP GET /count
     v
+-------------------+
|  API Gateway      |
|  (REST API)       |
+-------------------+
          |
          | invokes
          v
+-------------------+
|  AWS Lambda       |
|  (Python)         |
+-------------------+
          |
          | UpdateItem
          v
+-------------------+
|  DynamoDB         |
|  visitors table   |
+-------------------+

------------------------------------------------------------
FEATURES
------------------------------------------------------------

- Serverless (no EC2, no servers to manage)
- Visitor count stored persistently in DynamoDB
- Stateless Lambda function
- Public REST endpoint
- Low-cost, easy to tear down

------------------------------------------------------------
TECH STACK
------------------------------------------------------------

- AWS Lambda (Python 3.x)
- Amazon API Gateway (REST API)
- Amazon DynamoDB
- IAM (least-privilege role)
- GitHub (documentation & version control)

------------------------------------------------------------
DYNAMODB DESIGN
------------------------------------------------------------

Table Name: visitors

Primary Key:
- Partition Key: id (String)

Item Example:

id    : "counter"
count : 5

------------------------------------------------------------
LAMBDA FUNCTION LOGIC
------------------------------------------------------------

1. Receive HTTP request from API Gateway
2. Increment the visitor count in DynamoDB using UpdateItem
3. Read the updated value
4. Return the count as a JSON response

------------------------------------------------------------
LAMBDA CODE (REFERENCE)
------------------------------------------------------------

import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('visitors')

def lambda_handler(event, context):
    response = table.update_item(
        Key={'id': 'counter'},
        UpdateExpression='ADD #c :inc',
        ExpressionAttributeNames={'#c': 'count'},
        ExpressionAttributeValues={':inc': 1},
        ReturnValues='UPDATED_NEW'
    )

    return {
        'statusCode': 200,
        'body': json.dumps({
            'visitors': int(response['Attributes']['count'])
        })
    }

------------------------------------------------------------
API GATEWAY CONFIGURATION
------------------------------------------------------------

- API Type   : REST API
- Resource   : /count
- Method     : GET
- Integration: Lambda (proxy integration enabled)
- Stage      : prod

Invoke URL format:

https://<api-id>.execute-api.<region>.amazonaws.com/prod/count

Example Response:

{
  "visitors": 5
}

------------------------------------------------------------
STEP-BY-STEP SETUP (FOR RE-CREATION)
------------------------------------------------------------

1. Create DynamoDB Table
   - Table name: visitors
   - Partition key: id (String)

2. Create IAM Role for Lambda
   - DynamoDB UpdateItem permission
   - CloudWatch Logs access

3. Create Lambda Function
   - Runtime: Python 3.x
   - Attach IAM role
   - Add DynamoDB table name correctly (case-sensitive)

4. Test Lambda Manually
   - Use default test event
   - Verify count increments

5. Create API Gateway
   - REST API
   - Create resource: /count
   - Create GET method
   - Integrate with Lambda
   - Deploy to stage: prod

6. Test Public URL
   - Append /count (important)
   - Verify visitor count increments on refresh

------------------------------------------------------------
COMMON ISSUES & FIXES
------------------------------------------------------------

Issue: "Missing Authentication Token"
Fix  : Ensure /count is appended to the invoke URL

Issue: ResourceNotFoundException
Fix  : DynamoDB table name is case-sensitive (visitors != Visitors)

Issue: Lambda timeout or permission error
Fix  : Verify IAM role permissions for DynamoDB

------------------------------------------------------------
CLEANUP (IMPORTANT TO AVOID BILLING)
------------------------------------------------------------

Delete resources in this order:

1. API Gateway (REST API)
2. Lambda Function
3. DynamoDB Table
4. IAM Role (optional, if not reused)

------------------------------------------------------------
FUTURE IMPROVEMENTS
------------------------------------------------------------

- Terraform or CloudFormation for IaC
- Frontend (HTML + JS) hosted on S3
- CloudFront CDN
- Lambda environment variables
- CI/CD using GitHub Actions
- Add unit tests for Lambda

------------------------------------------------------------
WHY THIS PROJECT
------------------------------------------------------------

This project showcases:
- Serverless architecture
- AWS core services
- Debugging real-world issues
- Clean documentation
- Cost-aware cloud practices

## Known Issues / Next Steps

- The visitor counter on the static website currently shows "undefined" instead of the actual number.
- Likely cause: mismatch between Lambda JSON response and JavaScript parsing.
- Next steps:
  - Verify Lambda returns correct JSON with key `visitors`.
  - Ensure JS fetch parses the response correctly.
  - Confirm CORS headers are present and API Gateway OPTIONS method works.


## Frontend: Static Website on S3

This project includes a simple static website hosted on **Amazon S3**. The website displays the visitor count dynamically by calling the API Gateway endpoint.

### Steps to Create the S3 Website

1. **Create an S3 bucket**
   - Go to **S3 → Create bucket**
   - Give it a unique name (e.g., `rohit-visitor-counter`)
   - Select the same region as your Lambda function (e.g., `us-east-1`)
   - **Uncheck** "Block all public access" and acknowledge the warning

2. **Enable static website hosting**
   - Go to the bucket **Properties**
   - Scroll to **Static website hosting**
   - Enable hosting
   - Set **Index document**: `index.html`
   - Save changes
   - Copy the **Bucket website endpoint** (this is your frontend URL)

3. **Upload the static website**
   - Go to **Objects → Upload**
   - Upload `index.html`
   - Click **Upload**

4. **Make the bucket public**
   - Go to **Permissions → Bucket policy**
   - Paste the following, replacing `<your-bucket-name>`:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::<your-bucket-name>/*"
       }
     ]
   }

Test the website

Open the Bucket website endpoint in a browser

The page should load (counter may still show "undefined" if Lambda JSON parsing issue exists)


---

### Optional additions for interview notes

```md
### Known Issues / Next Steps

- Visitor counter currently shows "undefined"
- Cause: mismatch between Lambda response and JS parsing
- Next: fix JSON key name and ensure CORS headers

