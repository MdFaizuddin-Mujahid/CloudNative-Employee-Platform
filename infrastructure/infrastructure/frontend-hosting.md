# Frontend Hosting and Content Delivery

## 1. Overview

The frontend layer of the CloudNative Employee Platform uses Amazon S3 and Amazon CloudFront to host and distribute static application assets.

AWS WAF and AWS Certificate Manager complement this layer by providing web application traffic protection and HTTPS certificate management.

## 2. AWS Services

| Service | Responsibility |
|---|---|
| Amazon S3 | Stores static frontend files |
| Amazon CloudFront | Distributes frontend content |
| AWS WAF | Provides web application protection |
| AWS Certificate Manager | Manages the HTTPS certificate |

## 3. Amazon S3 — Static Frontend Hosting

Amazon S3 is used to store the application's static frontend assets.

**Responsibilities:**
- Store HTML, CSS, JavaScript, and other static files.
- Provide the origin content consumed by the CloudFront distribution.
- Separate frontend hosting from backend application processing.

## 4. Amazon CloudFront — Content Delivery

Amazon CloudFront acts as the distribution layer for frontend requests.

**Responsibilities:**
- Receive user requests for frontend content.
- Retrieve content from the configured S3 origin.
- Deliver frontend assets to users through the CloudFront distribution.
- Provide HTTPS viewer access.

CloudFront reduces the need for users to access the S3 origin directly.

## 5. AWS WAF — Web Application Protection

AWS WAF is integrated into the project's edge delivery architecture.

**Purpose:**
- Inspect supported
