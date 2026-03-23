# Project Helix Cloud Lab Series

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Duration](https://img.shields.io/badge/Each%20Lab-30%20Days-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Focus](https://img.shields.io/badge/Focus-Data%20Migration%20%7C%20IaC-informational)

## Executive Summary

The **Project Helix Cloud Lab Series** is a set of parallel, 30-day hands-on training modules that all simulate the same enterprise data migration scenario across different platforms. Each lab migrates data from a legacy, non-normalized flat-file / legacy database (**Source**) into a modern, normalized relational schema (**Target**) but uses platform-specific services and tooling.

All labs share the same storyline:

- Generate synthetic legacy data.
- Load it into a “legacy” store.
- Build an ETL pipeline (Extract → Transform → Load) to a modern, normalized schema.
- Automate infrastructure provisioning with Terraform.
- Perform verification, documentation, and a “Nuke & Pave” rebuild.

You can complete any single lab as a standalone project, or complete multiple labs to compare how local/Azure-style, AWS, and GCP approaches differ in practice.

---

## Lab Overview

| Lab                             | Platform / Stack                                   | Infra Target                          | ETL Engine     | Automation                    |
|---------------------------------|----------------------------------------------------|---------------------------------------|----------------|------------------------------|
| Core Helix (Local Containers)   | Local workstation, Docker, MySQL 8.0               | Docker containers (legacy/modern DB)  | Python         | Terraform + Ansible (WSL)    |
| Helix on AWS                    | AWS Free Tier (S3, RDS MySQL)                      | VPC, S3, RDS MySQL                    | Python         | Terraform + scripts          |
| Helix on GCP                    | Google Cloud Free Tier (Cloud Storage, Cloud SQL)  | VPC, Cloud Storage, Cloud SQL MySQL   | Python         | Terraform + scripts          |

> Note: The “core” Helix implementation (local Docker + Terraform + Ansible) can be used from any OS and conceptually maps well to Azure-style data migration and DevOps workflows.

---

## Shared Scenario: Data Migration & Normalization

All labs implement the same logical data model.

### Legacy Source (Flat File / Legacy Schema)

```text
RECORD_RAW
──────────────────────────────
raw_id           INT  (PK)
full_name        VARCHAR(255)   ← "LASTNAME, FIRSTNAME"
dob_string       VARCHAR(50)    ← Mixed formats (YYYYMMDD, YYYY-MM-DD, etc.)
category_string  TEXT           ← "CAT-A1,CAT-B2,CAT-C3" (CSV anti-pattern)
last_event_date  VARCHAR(50)
Modern Target (Normalized Relational Schema)
text
ENTITY_MASTER                           EVENT_LOG
──────────────────────────              ──────────────────────────────
EntityID   INT  (PK)  ◄──────────────  EntityID    INT  (FK)
FirstName  VARCHAR                      EventID     INT  (PK)
LastName   VARCHAR                      CategoryCode VARCHAR
DOB        DATE                         EventDate    DATE
LegacyID   INT  (traceability)
Key behaviors in every lab:

Normalization: Explode category_string into multiple EVENT_LOG rows (1:N).

Name parsing: Split "LASTNAME, FIRSTNAME" into separate fields.

Date standardization: Convert mixed date strings into DATE columns.

Verification: Compare row counts and spot check sample records post-migration.

Lab 1 – Core Project Helix (Local / “Azure-style”)
Folder: helix-core/

Purpose
Learn the migration workflow and core data-engineering skills without any cloud accounts, using local Docker containers for both legacy and modern databases.

Technical Architecture
Layer	Technology	Purpose
Infrastructure	Terraform + Docker Provider	Provision legacy and target MySQL DBs
Database	MySQL 8.0 (Docker containers)	Isolated legacy and modern instances
ETL Engine	Python (Pandas / SQLAlchemy)	Extract, transform, load pipeline
Automation	Ansible (WSL)	Workstation and environment configuration
Design	Visio / draw.io	ERD and migration diagrams
Highlights
No cloud billing risk; everything runs locally via Docker.

Strong focus on Terraform + Ansible automation patterns.

Ideal starting point before cloud-specific labs.

Lab 2 – Project AWS Helix (AWS + Terraform)
Folder: helix-aws/

Purpose
Rebuild the Helix migration on AWS Free Tier, using Terraform to provision S3 and RDS, and Python for ETL.

Technical Architecture
Layer	AWS Service / Tool	Purpose
Infrastructure	Terraform (AWS provider)	Provision VPC, subnets, security groups
Storage	Amazon S3	Store legacy CSV
Database	Amazon RDS for MySQL (legacy/modern schemas)	Host legacy and normalized schemas
ETL Engine	Python (Pandas, SQLAlchemy, boto3)	ETL from legacy to modern on RDS
Automation	Terraform + shell/PowerShell scripts	Deploy infra, run ETL, Nuke & Pave
Key Differences vs Core Helix
Where it runs: DBs are on RDS inside a VPC, not Docker on localhost.

Storage: Legacy CSV is persisted in S3 instead of only a local data/ folder.

Access & security: You must handle RDS networking, security groups, and credentials via Terraform and/or cloud secrets.

No Ansible: Automation is primarily Terraform + small scripts; fewer moving parts than the original Ansible layer.

Lab 3 – Project GCP Helix (GCP + Terraform)
Folder: helix-gcp/

Purpose
Rebuild the Helix migration on Google Cloud Platform, using Terraform to provision Cloud Storage and Cloud SQL, and Python for ETL.

Technical Architecture
Layer	GCP Service / Tool	Purpose
Infrastructure	Terraform (google provider)	Provision VPC, subnetwork, Cloud SQL, GCS
Storage	Cloud Storage	Store legacy CSV
Database	Cloud SQL for MySQL (legacy/modern schemas)	Host legacy and normalized schemas
ETL Engine	Python (Pandas, SQLAlchemy, GCS client)	ETL from legacy to modern on Cloud SQL
Automation	Terraform + shell/PowerShell scripts	Deploy infra, run ETL, Nuke & Pave
Key Differences vs Core Helix
Cloud SQL vs Docker: Legacy and modern DBs live in Cloud SQL, with connection management and service accounts.

Cloud Storage vs local: Legacy CSV lives in Cloud Storage instead of only local disk.

GCP IAM & networking: You learn about service accounts, VPC subnets, and Cloud SQL connectivity patterns.

Cross-Lab Differences
Platform Services
Concern	Core Helix (Local)	AWS Lab	GCP Lab
Legacy storage	Local CSV	S3 object	Cloud Storage object
Legacy DB	Docker MySQL container	RDS MySQL (legacy schema)	Cloud SQL MySQL (legacy schema)
Target DB	Docker MySQL container	RDS MySQL (modern schema)	Cloud SQL MySQL (modern schema)
Infra IaC	Terraform + Docker provider	Terraform (AWS provider)	Terraform (google provider)
Config mgmt	Ansible (workstation/bootstrap)	CLI + shell scripts	gcloud + shell scripts
Skills Emphasis
Core Helix: local DevOps, Terraform + Ansible, container-based DBs, core ETL logic.

AWS Helix: AWS fundamentals, VPC, S3, RDS, IAM basics, Terraform on AWS.

GCP Helix: GCP fundamentals, VPC networking, Cloud Storage, Cloud SQL, service accounts, Terraform on GCP.

How to Use This Repository
Pick a lab:

New to Helix → start with Core Helix (helix-core/).

Want cloud fundamentals → pick AWS (helix-aws/) or GCP (helix-gcp/).

Follow the 30‑day calendar in that lab’s README. The calendars define outcomes and checkpoints; you are expected to research missing details and work through issues yourself.

Repeat on another platform to build a multi-cloud migration story for interviews and your portfolio.

Extend the design:

Add monitoring/alerting (e.g., CloudWatch or Cloud Monitoring).

Add CI/CD.

Swap MySQL for Postgres or another engine in a variant.

Repository Layout
bash
project-helix-cloud-series/
├── helix-core/       # Original Helix (local Docker + Terraform + Ansible)
├── helix-aws/        # AWS Helix (S3 + RDS + Terraform)
├── helix-gcp/        # GCP Helix (Cloud Storage + Cloud SQL + Terraform)
└── README.md         # This overview file
Each lab folder contains its own README.md, infrastructure/, scripts/, data/, and docs/. The READMEs are foundations, not step-by-step tutorials: they give you the architecture, goals, and main commands; you are responsible for filling in the gaps, troubleshooting, and making implementation choices.
