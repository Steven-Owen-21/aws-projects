# Cloud Resume - Portfolio Website

A serverless portfolio website built entirely on AWS, demonstrating cloud engineering skills including Infrastructure as Code, CI/CD, and security best practices.

🌐 **Live site:** [stevenowenaws.com](https://stevenowenaws.com)

---

## Architecture

```mermaid
graph TD
    %% Styling Definitions
    classDef client fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef dns fill:#232F3E,stroke:#fff,stroke-width:1px,color:#fff;
    classDef cdn fill:#FF9900,stroke:#333,stroke-width:1px,color:#fff;
    classDef storage fill:#3F8624,stroke:#333,stroke-width:1px,color:#fff;
    classDef api fill:#A1662F,stroke:#333,stroke-width:1px,color:#fff;
    classDef compute fill:#D13212,stroke:#333,stroke-width:1px,color:#fff;
    classDef database fill:#527FFF,stroke:#333,stroke-width:1px,color:#fff;
    classDef email fill:#E24A34,stroke:#333,stroke-width:1px,color:#fff;

    %% Nodes
    User["💻 Users / Browser"]:::client
    R53["🌐 Route 53 (DNS)<br>stevenowenaws.com"]:::dns
    CF["⚡ CloudFront (CDN + HTTPS)"]:::cdn
    
    S3["🪣 S3 Bucket (Static Website)<br>• index.html<br>• steven_owen_cv.html<br>• profile.jpg<br>• certs/"]:::storage
    
    APIGW["🔌 API Gateway (HTTP)<br>portfolio-api<br>GET /count | POST /contact"]:::api
    
    LambdaCount["⚙️ Lambda:<br>visitor-counter"]:::compute
    LambdaContact["⚙️ Lambda:<br>contact-form"]:::compute
    
    Dynamo["🗄️ DynamoDB<br>visitor-counter"]:::database
    SES["✉️ SES (Email)<br>Sends to owner"]:::email

    %% Connections
    User --> R53
    R53 --> CF
    CF --> S3
    CF --> APIGW
    
    APIGW -->|GET /count| LambdaCount
    APIGW -->|POST /contact| LambdaContact
    
    LambdaCount --> Dynamo
    LambdaContact --> SES
```

---

## CI/CD Pipeline

```
┌──────────────┐     ┌───────────────────┐     ┌─────────────┐     ┌────────────┐
│  Developer   │────▶│  GitHub Actions   │────▶│  S3 Sync    │────▶│ CloudFront │
│  git push    │     │  (OIDC Auth)      │     │  Upload     │     │ Invalidate │
└──────────────┘     └───────────────────┘     └─────────────┘     └────────────┘
```

- Push to `main` branch triggers automatic deployment
- Uses OIDC identity federation (no stored access keys)
- Syncs files to S3 and invalidates CloudFront cache

---

## Security Measures

- **CORS** locked to `stevenowenaws.com` only
- **API throttling** — rate limited to prevent abuse
- **IAM least privilege** — Lambda roles scoped to specific actions
- **OIDC** — no long-lived credentials for CI/CD
- **Honeypot field** — bot protection on contact form
- **Input validation** — client and server-side length limits
- **HTTPS** enforced via CloudFront

---

## Tech Stack

| Service | Purpose |
|---------|---------|
| S3 | Static website hosting |
| CloudFront | CDN, HTTPS, caching |
| Route 53 | DNS management |
| API Gateway | HTTP API with throttling |
| Lambda | Serverless compute (Python) |
| DynamoDB | Visitor counter storage |
| SES | Contact form email delivery |
| IAM | Least privilege access control |
| GitHub Actions | CI/CD with OIDC |
| CloudFormation | Infrastructure as Code |

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Portfolio homepage |
| `steven_owen_cv.html` | CV/Resume page |
| `portfolio-infrastructure.yaml` | CloudFormation template |
| `.github/workflows/deploy.yml` | CI/CD pipeline |

---

Built by Steven Owen as part of the AWS Cloud Resume Challenge.
