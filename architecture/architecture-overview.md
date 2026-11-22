# Architecture Overview

## 1. System Architecture

The CloudNative Employee Platform follows a serverless, API-driven architecture built using managed AWS services.

The system separates frontend delivery, API management, backend processing, and persistent data storage into distinct layers. This separation makes the application easier to maintain and extend.

## 2. Architecture Layers

### Layer 1 — Edge Delivery and Frontend Hosting

**Services:** Amazon CloudFront, Amazon S3, AWS WAF, AWS Certificate Manager

- Amazon S3 stores the static frontend assets.
- Amazon CloudFront distributes frontend content to users.
- AWS WAF provides web application traffic protection.
- AWS Certificate Manager manages the certificate used for HTTPS.

**Request flow:** Browser → CloudFront → S3

### Layer 2 — API Management

**Service:** Amazon API Gateway (REST API)

API Gateway provides the backend entry point for frontend operations.

The application uses two HTTP methods:

| Method | Operation |
|---|---|
| GET | Retrieve employee records |
| POST | Insert employee records |

The frontend browser sends API requests directly to the API Gateway endpoint.

**Request flow:** Browser → API Gateway

### Layer 3 — Serverless Compute

**Service:** AWS Lambda

Lambda executes the backend business logic without requiring dedicated application servers.

Two functions are configured:

**`getEmployees`**
- Handles GET requests.
- Retrieves employee records from DynamoDB.
- Returns the retrieved data through the API.

**`insertEmployeeData`**
- Handles POST requests.
- Processes incoming employee information.
- Writes employee records to DynamoDB.

**Request flow:** API Gateway → Lambda

### Layer 4 — Data Persistence

**Service:** Amazon DynamoDB

DynamoDB stores employee records in a table named `employeeData`.

| Configuration | Value |
|---|---|
| Table | `employeeData` |
| Partition key | `employeeid` |
| Database type | NoSQL |

The Lambda functions interact with DynamoDB to perform the application's data operations.

**Request flow:** Lambda → DynamoDB

### Layer 5 — Identity and Access Management

**Service:** AWS IAM

An IAM execution role named `serverless-lambda-role` is associated with the Lambda functions.

The role provides permissions for backend functions to access DynamoDB.

The implemented role uses the AWS-managed `AmazonDynamoDBFullAccess` policy. This is broader than required for the application's operations; a custom least-privilege policy is a recommended improvement.

---

## 3. End-to-End Request Lifecycle

### Employee Retrieval — GET

1. The user opens the application through CloudFront.
2. CloudFront delivers the frontend assets from S3.
3. The browser sends a GET request to API Gateway.
4. API Gateway invokes the `getEmployees` Lambda function.
5. Lambda retrieves employee records from DynamoDB.
6. The API response is returned to the browser.

### Employee Insertion — POST

1. The user submits employee information through the frontend.
2. The browser sends a POST request to API Gateway.
3. API Gateway invokes the `insertEmployeeData` Lambda function.
4. Lambda processes the incoming employee data.
5. Lambda writes the record to DynamoDB.
6. The API response is returned to the browser.

---

## 4. Architectural Design Decisions

| Design Decision | Reason |
|---|---|
| Static frontend on S3 | Avoids managing frontend application servers |
| CloudFront distribution | Provides an edge-based content delivery layer |
| API Gateway | Separates API management from backend execution |
| Lambda functions | Enables event-driven, serverless backend processing |
| DynamoDB | Provides managed NoSQL data storage |
| IAM execution role | Controls permissions
