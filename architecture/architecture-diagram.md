# CloudNative Employee Platform — Architecture Diagram

## 1. High-Level Architecture

The following diagram illustrates the logical architecture and service interactions of the application.

```mermaid
flowchart TB
    USER["Users / Web Browser"]

    subgraph EDGE["EDGE DELIVERY & SECURITY"]
        WAF["AWS WAF"]
        ACM["AWS Certificate Manager"]
        CF["Amazon CloudFront"]
    end

    subgraph FRONTEND["FRONTEND LAYER"]
        S3["Amazon S3<br/>Static Website Assets"]
    end

    subgraph API_LAYER["API MANAGEMENT LAYER"]
        APIGW["Amazon API Gateway<br/>REST API"]
        GET["GET Method"]
        POST["POST Method"]
    end

    subgraph BACKEND["SERVERLESS COMPUTE LAYER"]
        GETL["AWS Lambda<br/>getEmployees"]
        POSTL["AWS Lambda<br/>insertEmployeeData"]
        IAM["IAM Execution Role"]
    end

    subgraph DATA["DATA PERSISTENCE LAYER"]
        DDB[("Amazon DynamoDB<br/>employeeData")]
    end

    USER -->|"HTTPS"| CF
    WAF -.->|"Traffic filtering"| CF
    ACM -.->|"TLS certificate"| CF
    CF -->|"Origin request"| S3

    USER -->|"GET / POST API calls"| APIGW
    APIGW --> GET
    APIGW --> POST

    GET --> GETL
    POST --> POSTL

    IAM -.->|"Execution permissions"| GETL
    IAM -.->|"Execution permissions"| POSTL

    GETL -->|"Read records"| DDB
    POSTL -->|"Write records"| DDB
```

## 2. Request Flow

### Frontend Delivery

```mermaid
sequenceDiagram
    participant U as User Browser
    participant CF as CloudFront
    participant S3 as Amazon S3

    U->>CF: Request frontend
    CF->>S3: Retrieve static assets
    S3-->>CF: Return frontend content
    CF-->>U: Deliver frontend
```

### Employee Data Retrieval

```mermaid
sequenceDiagram
    participant U as User Browser
    participant API as API Gateway
    participant L as getEmployees Lambda
    participant DB as DynamoDB

    U->>API: GET request
    API->>L: Invoke function
    L->>DB: Read employee records
    DB-->>L: Return records
    L-->>API: Return result
    API-->>U: API response
```

### Employee Data Insertion

```mermaid
sequenceDiagram
    participant U as User Browser
    participant API as API Gateway
    participant L as insertEmployeeData Lambda
    participant DB as DynamoDB

    U->>API: POST request
    API->>L: Invoke function
    L->>DB: Write employee record
    DB-->>L: Return operation result
    L-->>API: Return result
    API-->>U: API response
```

## 3. Component Responsibilities

| Component | Function |
|---|---|
| CloudFront | Frontend content distribution |
| S3 | Static frontend hosting |
| WAF | Web application traffic protection |
| ACM | HTTPS certificate management |
| API Gateway | REST API entry point |
| Lambda | Backend request processing |
| DynamoDB | Employee data persistence |
| IAM | Backend execution permissions |

## 4. Architecture Notes

- Frontend delivery and API processing are separate request paths.
- The browser communicates with CloudFront for frontend assets.
- The browser communicates directly with API Gateway for employee operations.
- Lambda functions access DynamoDB using their execution role.
- Route 53 is not included because it was not implemented in this project.

**Diagram scope:** This represents the logical service architecture. It does not claim unverified VPC, subnet, origin access control, or network-isolation configurations.
