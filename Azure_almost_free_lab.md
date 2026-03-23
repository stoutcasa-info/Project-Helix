# Project Nimbus: AWS Free-Tier Serverless Web App

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Duration](https://img.shields.io/badge/Duration-30%20Days-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Platform](https://img.shields.io/badge/Platform-AWS%20Free%20Tier-informational)

## Executive Summary

**Project Nimbus** is a self-paced, 30-day hands-on training module that teaches core AWS services by building a small, production-style serverless web application entirely on the AWS Free Tier.

You will create a static front-end hosted on Amazon S3 that interacts with a backend API built using Amazon API Gateway, AWS Lambda, and Amazon DynamoDB. The project emphasizes security, monitoring, automation, and cost awareness.

> **Estimated Time:** ~1–1.5 hours/day  
> **Theme:** AWS Fundamentals, Serverless, Infrastructure as Code, and Cost-Aware Design

---

## Table of Contents

- [Technical Architecture](#technical-architecture)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [Module Breakdown](#module-breakdown)
  - [Week 1 — Foundations](#week-1--foundations)
  - [Week 2 — Compute--api--data](#week-2--compute--api--data)
  - [Week 3 — Automation--observability](#week-3--automation--observability)
  - [Week 4 — Hardening--documentation](#week-4--hardening--documentation)
- [Key AWS Concepts](#key-aws-concepts)
- [Verification](#verification)
- [30-Day Calendar](#30-day-calendar)
- [Contributing](#contributing)
- [License](#license)

---

## Technical Architecture

| Layer            | AWS Service(s)                  | Purpose                                             |
|-----------------|----------------------------------|-----------------------------------------------------|
| Front End       | S3 Static Website (+ CloudFront)| Host static HTML/CSS/JS web application            |
| API             | API Gateway                     | HTTPS entrypoint for client requests                |
| Compute         | AWS Lambda                      | Serverless business logic execution                 |
| Data Store      | DynamoDB                        | NoSQL storage for application data                  |
| Storage & Logs  | S3, CloudWatch Logs             | File storage, log retention, and exports           |
| Security        | IAM, CloudTrail                 | Identity, access control, and audit logging        |
| Observability   | CloudWatch Metrics & Alarms     | Monitoring, dashboards, and alerts                 |
| IaC             | CloudFormation                  | Declarative, reproducible infrastructure           |

---

## Prerequisites

Before starting **Day 1**, ensure you have:

- An [AWS Free Tier](https://aws.amazon.com/free/) account with billing alerts configured.
- Basic familiarity with Git and a GitHub account.
- A local development environment with:
  - Node.js or Python (for Lambda)
  - AWS CLI configured (`aws configure`)
  - An editor like VS Code (with AWS Toolkit recommended)

> **Cost Note:** This project is designed to stay within the AWS Free Tier when used in a personal, low-traffic learning environment. Always monitor your usage and clean up resources when not in use.

---

## Project Structure

```bash
project-nimbus/
├── infrastructure/
│   └── template.yaml          # CloudFormation template for core resources
├── frontend/
│   ├── index.html             # Static web app
│   ├── app.js                 # API integration logic
│   └── styles.css
├── lambda/
│   └── handler.py             # Lambda function source
├── docs/
│   ├── architecture-diagram.png
│   └── verification-notes.md
└── README.md
Quick Start
These steps assume you already have an AWS account, the AWS CLI configured, and access to CloudFormation.

1. Deploy Core Infrastructure
From the infrastructure directory:

bash
aws cloudformation deploy \
  --template-file template.yaml \
  --stack-name project-nimbus-stack \
  --capabilities CAPABILITY_NAMED_IAM
This creates the S3 bucket, DynamoDB table, Lambda function, API Gateway, and IAM roles.

2. Upload the Front-End
bash
aws s3 sync ./frontend s3://<your-frontend-bucket-name> --delete
Enable static website hosting on the bucket and note the website endpoint.

3. Configure Front-End to Use API
Update frontend/app.js with your API Gateway endpoint URL, then re-sync the front-end to S3.

4. Test the Application
Open the S3 website endpoint in your browser.

Submit a form or trigger the app action.

Verify data is written to DynamoDB and logs appear in CloudWatch Logs.

Module Breakdown
Week 1 — Foundations
Day 1: Create AWS account, enable billing alerts, secure root user, create admin IAM user.

Day 2: Explore AWS console, Regions/AZs, and Free Tier usage page.

Day 3: Create IAM roles and least-privileged permissions for Lambda and deployment.

Day 4: Create an S3 bucket; practice uploads, versioning, and encryption settings.

Day 5: Turn S3 bucket into a static website; deploy a simple HTML page.

Weekend: Clean up IAM entities, verify billing alarms, and enhance the static site UI.

Week 2 — Compute / API / Data
Day 8: Create a basic Lambda function and inspect logs in CloudWatch.

Day 9: Expose Lambda with API Gateway; test via browser/Postman.

Day 10: Create a DynamoDB table and perform basic CRUD via the console.

Day 11: Connect Lambda to DynamoDB for read/write operations.

Day 12: Wire the front-end to your API using JavaScript (fetch/AJAX).

Weekend: Perform end-to-end testing and refine error handling.

Week 3 — Automation / Observability
Day 15: Build CloudWatch dashboards and alarms for Lambda and API errors.

Day 16: Review VPC, subnets, route tables, and security groups (diagram your environment).

Day 17: Create an initial CloudFormation template that provisions an S3 bucket and IAM role.

Day 18: Expand CloudFormation to include DynamoDB, Lambda, and API Gateway.

Day 19: Study CI/CD concepts with CodePipeline/CodeBuild (no full pipeline required).

Day 20: Review Cost Explorer and Free Tier usage; confirm resources remain within limits.

Weekend: Delete and fully rebuild all resources using CloudFormation only.

Week 4 — Hardening / Documentation
Day 22: Tighten IAM policies and enable CloudTrail for auditing.

Day 23: Configure DynamoDB backups (where free) and S3 lifecycle rules for logs.

Day 24: Perform light load testing; monitor metrics and scale behavior.

Day 25: Draft detailed architecture and operations documentation in /docs.

Day 26: Identify security and resilience improvements; record them in verification-notes.md.

Day 27: Practice a 3–5 minute verbal walkthrough as if in a technical interview.

Day 28: Polish CloudFormation templates and code; add comments and clean structure.

Day 29: Final cleanup, tagging, and sanity checks across the AWS console.

Day 30: Publish to GitHub, optionally record a short demo video, and link it in this README.

Key AWS Concepts
Concept	Where Applied
IAM & Least Privilege	IAM users, roles, and policies
Serverless Architecture	Lambda + API Gateway
NoSQL Data Modeling	DynamoDB table design
Static Website Hosting	S3 static hosting
Observability & Logging	CloudWatch metrics, logs, and alarms
Infrastructure as Code (IaC)	CloudFormation template.yaml
Cost Optimization & Free Tier	Billing alarms, Free Tier usage
Security & Compliance Basics	CloudTrail, encryption, access logs
Verification
By the end of the project, you should be able to:

Access the S3-hosted website and successfully submit data via the UI.

Confirm data is persisted in DynamoDB and visible via the AWS console or CLI.

View Lambda invocation logs and any errors in CloudWatch Logs.

Trigger CloudWatch alarms by inducing controlled errors and confirm notifications are received (if configured).

Tear down and fully rebuild the environment using CloudFormation with minimal manual steps.

Any failure in the above checks indicates an issue with configuration, permissions, or networking. Start troubleshooting with:

IAM role and policy permissions.

CloudFormation stack events and resource statuses.

CloudWatch Logs for Lambda and API Gateway.

30-Day Calendar
Week	Days	Focus
Week 1	1–5	Account setup, IAM, S3 static hosting
Week 2	8–12	Lambda, API Gateway, DynamoDB, front-end wiring
Week 3	15–20	Observability, CloudFormation, cost management
Week 4	22–30	Hardening, verification, documentation, portfolio
Contributing
Pull requests are welcome. For significant changes, please open an issue first to discuss what you would like to modify.

Fork the repository

Create a feature branch (git checkout -b feature/your-improvement)

Commit your changes (git commit -m 'Add improvement')

Push to the branch (git push origin feature/your-improvement)

Open a Pull Request

License
This project is licensed under the MIT License. See the LICENSE file for details.

