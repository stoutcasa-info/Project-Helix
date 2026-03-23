# Project Helix Cloud Lab Series

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Duration](https://img.shields.io/badge/Each%20Lab-30%20Days-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Focus](https://img.shields.io/badge/Focus-Data%20Migration%20%7C%20IaC-informational)

---

## Executive Summary

The **Project Helix Cloud Lab Series** is a set of parallel, 30-day hands-on training modules that simulate the same enterprise data migration scenario across different platforms. Each lab migrates data from a legacy, non-normalized flat-file / legacy database (**Source**) into a modern, normalized relational schema (**Target**) using platform-specific services and tooling.

‼️**Recommend creating a checklist to follow so you're not lost in the process**‼️

All labs share the same storyline:

- Generate synthetic legacy data.
- Load it into a "legacy" store.
- Build an ETL pipeline (Extract → Transform → Load) to a modern, normalized schema.
- Automate infrastructure provisioning with Terraform.
- Perform verification, documentation, and a "Nuke & Pave" rebuild.

You can complete any single lab as a standalone project, or complete multiple labs to compare how **Azure**, **local**, **AWS**, and **GCP** approaches differ in practice.

---

## Lab Overview

| Lab | Folder / README | Platform / Stack | Infra Target | ETL Engine | Automation |
|-----|-----------------|------------------|--------------|------------|------------|
| Helix on Azure | `helix-azure/` → `Project_Helix_Azure_lab.md` | Azure Free/Sandbox, Azure Database for MySQL | Azure RG, VNet, Azure MySQL Flexible Server | Python | Terraform + Ansible (WSL) |
| Core Helix (Local Containers) | `helix-core/` → `README.md` | Local workstation, Docker, MySQL 8.0 | Docker containers (legacy/modern DB) | Python | Terraform + Ansible (WSL) |
| Helix on AWS | `helix-aws/` → `README.md` | AWS Free Tier (S3, RDS MySQL) | VPC, S3, RDS MySQL | Python | Terraform + shell/PowerShell |
| Helix on GCP | `helix-gcp/` → `README.md` | GCP Free Tier (Cloud Storage, Cloud SQL MySQL) | VPC, Cloud Storage, Cloud SQL (MySQL) | Python | Terraform + shell/PowerShell |

> READMEs are **foundations**, not step-by-step tutorials. They provide the architecture, goals, and main commands — you are expected to fill in the gaps, research details, and troubleshoot on your own.

---

## Shared Scenario: Data Migration & Normalization

All labs implement the same logical data model.

### Legacy Source Schema

```text
RECORD_RAW
──────────────────────────────────────
raw_id           INT  (PK)
full_name        VARCHAR(255)   ← "LASTNAME, FIRSTNAME"
dob_string       VARCHAR(50)    ← Mixed formats (YYYYMMDD, YYYY-MM-DD, etc.)
category_string  TEXT           ← "CAT-A1,CAT-B2,CAT-C3" (CSV anti-pattern)
last_event_date  VARCHAR(50)
```

### Modern Target Schema

```text
ENTITY_MASTER                           EVENT_LOG
──────────────────────────────          ──────────────────────────────
EntityID   INT  (PK)  ◄──────────────  EntityID    INT  (FK)
FirstName  VARCHAR                      EventID     INT  (PK)
LastName   VARCHAR                      CategoryCode VARCHAR
DOB        DATE                         EventDate    DATE
LegacyID   INT  (traceability)
```

### Core Transformation Behaviors

| Behavior | Description |
|----------|-------------|
| Normalization | Explode `category_string` into multiple `EVENT_LOG` rows (1:N) |
| Name Parsing | Split `"LASTNAME, FIRSTNAME"` into separate `FirstName` / `LastName` fields |
| Date Standardization | Convert mixed date strings into typed `DATE` columns |
| Verification | Compare row counts and spot-check sample records after migration |

---

## Lab: Project Helix — Azure Edition

**Folder:** `helix-azure/`  
**Detailed README:** `helix-azure/Project_Helix_Azure_lab.md`

### Purpose

Simulate a real-world enterprise migration on Microsoft Azure, moving from a legacy flat-file schema into a normalized schema on Azure Database for MySQL – Flexible Server. Uses Terraform for infrastructure and Python for ETL. Ansible on WSL automates local workstation setup and creates a repeatable environment.

### Technical Architecture

| Layer | Technology / Service | Purpose |
|-------|----------------------|---------|
| Cloud Provider | Azure Free Account / Sandbox | Host managed MySQL and networking |
| Database | Azure Database for MySQL – Flexible Server | Legacy and modern schemas on a single server |
| Infrastructure as Code | Terraform (azurerm provider) | Provision and tear down Azure resources |
| ETL Engine | Python (Pandas / SQLAlchemy) | Extract, transform, and load between schemas |
| Automation | Ansible on WSL 2 | Workstation configuration and repeatable setup |
| Design | Visio / draw.io | ER and migration diagrams |

### Notes

- Replaces local Docker MySQL with Azure-managed MySQL.
- Keeps the Helix 30-day structure: Azure foundations, migration, automation, verification, portfolio.
- See `Project_Helix_Azure_lab.md` for the full 30-day breakdown and specific commands.

---

## Lab: Core Project Helix — Local / Baseline

**Folder:** `helix-core/`  
**Detailed README:** `helix-core/README.md`

### Purpose

Learn the migration workflow and core data-engineering skills without any cloud dependencies. Uses local Docker containers for both legacy and modern databases. This is the baseline Helix implementation and conceptually maps well to Azure/DevOps patterns.

### Technical Architecture

| Layer | Technology | Purpose |
|-------|------------|---------|
| Infrastructure | Terraform + Docker provider | Provision legacy and target MySQL DBs |
| Database | MySQL 8.0 (Docker containers) | Isolated legacy and modern instances |
| ETL Engine | Python (Pandas / SQLAlchemy) | Extract, transform, load pipeline |
| Automation | Ansible (WSL) | Workstation and environment configuration |
| Design | Visio / draw.io | ERD and migration diagrams |

### Notes

- No cloud billing risk; everything runs locally via Docker.
- Strong focus on Terraform + Ansible automation.
- Ideal starting point before cloud-specific labs.

---

## Lab: Project Helix — AWS Edition

**Folder:** `helix-aws/`  
**Detailed README:** `helix-aws/README.md`

### Purpose

Rebuild the Helix migration on AWS Free Tier using Terraform to provision S3 and RDS, with Python for ETL. Covers AWS fundamentals (VPC, S3, RDS, IAM) while maintaining the same migration storyline.

### Technical Architecture

| Layer | AWS Service / Tool | Purpose |
|-------|-------------------|---------|
| Infrastructure | Terraform (AWS provider) | Provision VPC, subnets, security groups |
| Storage | Amazon S3 | Store legacy CSV |
| Database | Amazon RDS for MySQL (legacy/modern schemas) | Host legacy and normalized schemas |
| ETL Engine | Python (Pandas, SQLAlchemy, boto3) | ETL from legacy to modern on RDS |
| Automation | Terraform + shell/PowerShell scripts | Deploy infra, run ETL, Nuke & Pave |

### Notes

- Databases run on RDS inside a VPC, not on localhost.
- Legacy CSV is persisted in S3 in addition to the local `data/` folder.
- Focuses on AWS networking, IAM basics, and cost-aware use of Free Tier.

---

## Lab: Project Helix — GCP Edition

**Folder:** `helix-gcp/`  
**Detailed README:** `helix-gcp/README.md`

### Purpose

Rebuild the Helix migration on Google Cloud Platform using Terraform to provision Cloud Storage and Cloud SQL, with Python for ETL. Covers GCP fundamentals while implementing the same migration and verification steps.

### Technical Architecture

| Layer | GCP Service / Tool | Purpose |
|-------|-------------------|---------|
| Infrastructure | Terraform (google provider) | Provision VPC, subnetwork, Cloud SQL, GCS |
| Storage | Cloud Storage | Store legacy CSV |
| Database | Cloud SQL for MySQL (legacy/modern schemas) | Host legacy and normalized schemas |
| ETL Engine | Python (Pandas, SQLAlchemy, GCS client) | ETL from legacy to modern on Cloud SQL |
| Automation | Terraform + shell/PowerShell scripts | Deploy infra, run ETL, Nuke & Pave |

### Notes

- Databases run on Cloud SQL with VPC networking and service accounts.
- Legacy CSV lives in Cloud Storage rather than only on local disk.
- Emphasizes GCP IAM, networking, and Cloud SQL connectivity.

---

## Cross-Lab Differences

### Platform Services

| Concern | Azure Lab | Core Helix (Local) | AWS Lab | GCP Lab |
|---------|-----------|-------------------|---------|---------|
| Legacy storage | Local CSV (Azure context) | Local CSV | S3 object | Cloud Storage object |
| Legacy DB | Azure MySQL Flexible Server | Docker MySQL container | RDS MySQL (legacy schema) | Cloud SQL MySQL (legacy schema) |
| Target DB | Azure MySQL Flexible Server | Docker MySQL container | RDS MySQL (modern schema) | Cloud SQL MySQL (modern schema) |
| Infra IaC | Terraform (azurerm provider) | Terraform + Docker provider | Terraform (AWS provider) | Terraform (google provider) |
| Config mgmt | Ansible (WSL) | Ansible (WSL) | CLI + shell/PowerShell | gcloud + shell/PowerShell |

### Skills Emphasis

| Lab | Primary Focus Areas |
|-----|---------------------|
| Azure Helix | Azure fundamentals, Azure Database for MySQL, Terraform on Azure, Ansible-driven workstation automation |
| Core Helix | Local DevOps, Terraform + Ansible, container-based DBs, core ETL logic |
| AWS Helix | AWS fundamentals, VPC, S3, RDS, IAM basics, Terraform on AWS |
| GCP Helix | GCP fundamentals, VPC networking, Cloud Storage, Cloud SQL, service accounts, Terraform on GCP |

---

## How to Use This Repository

### Step 1 — Pick a Lab

| Goal | Recommended Lab |
|------|-----------------|
| Azure-focused skills | Helix on Azure → `helix-azure/Project_Helix_Azure_lab.md` |
| Cloud-agnostic baseline | Core Helix → `helix-core/README.md` |
| AWS skills | Helix on AWS → `helix-aws/README.md` |
| GCP skills | Helix on GCP → `helix-gcp/README.md` |

### Step 2 — Follow the 30-Day Calendar

Each lab README defines a 30-day calendar with outcomes and checkpoints. You are expected to research missing details and work through issues independently.

### Step 3 — Repeat Across Platforms (Optional)

Complete multiple labs to build a **multi-cloud migration story** for interviews and your portfolio.

### Step 4 — Extend the Design

Consider these enhancements to deepen your skills:

- Add monitoring/alerting (Azure Monitor, CloudWatch, Cloud Monitoring, etc.)
- Add CI/CD (GitHub Actions, Azure DevOps, CodePipeline, Cloud Build, etc.)
- Swap MySQL for PostgreSQL or another engine in a variant

---

## Repository Layout

```bash
project-helix-cloud-series/
├── helix-azure/      # Azure Helix (Azure MySQL + Terraform + Ansible)
│   └── Project_Helix_Azure_lab.md
├── helix-core/       # Core/local Helix (Docker + Terraform + Ansible)
│   └── README.md
├── helix-aws/        # AWS Helix (S3 + RDS + Terraform)
│   └── README.md
├── helix-gcp/        # GCP Helix (Cloud Storage + Cloud SQL + Terraform)
│   └── README.md
└── README.md         # This overview file
```

Each lab folder contains its own README and subfolders such as `infrastructure/`, `scripts/`, `data/`, and `docs/`. 
These READMEs are deliberately foundational, not exhaustive — they set direction and expectations, but you are responsible for figuring out the finer details.
✅AI is your friend, bur learing is hands on and figuring out mistakes goes along way.
