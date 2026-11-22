# API Gateway — REST API Configuration

## 1. Overview

Amazon API Gateway provides the API management layer for the CloudNative Employee Platform.

It exposes REST API endpoints that allow the frontend application to communicate with the serverless backend. API Gateway receives HTTP requests and invokes the corresponding AWS Lambda function.

## 2. Architecture Position

```text
User Browser
     |
     | GET / POST
     v
Amazon API Gateway
     |
     +----------------------+
     |                      |
     v                      v
GET Method              POST Method
     |                      |
     v                      v
getEmployees          insertEmployeeData
     |                      |
     +----------+-----------+
                |
                v
         Amazon DynamoDB
```

## 3. API Methods and Integrations

The application uses two HTTP methods.

| HTTP Method | Lambda Integration | Operation |
|---|---|---|
| GET | `getEmployees` | Retrieve employee records |
| POST | `insertEmployeeData` | Insert employee records |

### GET Method

The GET method handles employee retrieval requests.

**Execution flow:**

1. The frontend sends a GET request to the API endpoint.
2. API Gateway receives the request.
3. API Gateway invokes `getEmployees`.
4. Lambda retrieves employee records from DynamoDB.
5. The function returns the result through API Gateway.

### POST Method

The POST method handles employee insertion
