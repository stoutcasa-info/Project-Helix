Project_GCP_Helix/
├── infrastructure/
│   └── main.tf              # Terraform for VPC, Cloud SQL, Cloud Storage
├── scripts/
│   ├── generate_data.py     # Create legacy CSV and upload to GCS
│   ├── migration_engine.py  # ETL: legacy → modern on Cloud SQL
│   └── verify_migration.py  # Data quality checks
├── data/
│   └── legacy_source_data.csv
├── docs/
│   └── Architecture_GCP_Migration.png
└── README.md
30-Day GCP Helix Training Plan
Week 1 — Foundations (GCP + Schema)
Day 1 – GCP account & tools

Create a Google Cloud Free Tier account, enable billing and budget alerts.

Install: gcloud SDK, Terraform, Python, and your editor.

Day 2 – Project & IAM

Create a dedicated GCP project for this lab, enable Cloud SQL and Cloud Storage APIs.

Set up a service account for Terraform and local scripts (with least privileges).

Day 3 – Schema & diagrams

Design legacy vs modern schemas (same as Helix: RECORD_RAW, ENTITY_MASTER, EVENT_LOG).
​

Draw ER diagram and save as docs/Architecture_GCP_Migration.png.

Day 4 – Terraform networking & storage

Write infrastructure/main.tf with:

GCP provider, project, region.

VPC, subnetwork.

Cloud Storage bucket for legacy CSV (use free tier limits).

Day 5 – Terraform Cloud SQL

Extend main.tf to create a Cloud SQL MySQL instance and database(s) (legacy/modern).

Output connection info (instance connection name / IP).

Weekend (6–7)

Run terraform apply, confirm Cloud Storage bucket and Cloud SQL are created.

Connect to Cloud SQL from a client (using public IP or Cloud SQL Proxy) and test a simple query.

Week 2 — The Migration
Day 8 – Generate legacy data

Implement generate_data.py (same structure as Helix): create legacy_source_data.csv with: name, mixed date formats, comma-separated categories.
​

Upload CSV to Cloud Storage using google-cloud-storage.

Day 9 – Load into legacy schema

Create legacy database and RECORD_RAW table on Cloud SQL (via SQL script or Python helper).

Load CSV into RECORD_RAW using Python inserts or MySQL LOAD DATA LOCAL INFILE from your machine.

Day 10 – Extract

In migration_engine.py, connect to Cloud SQL (MySQL driver + SQLAlchemy) and extract all rows from legacy.RECORD_RAW into Pandas.

Day 11 – Transform

Implement transformation logic identical to Helix:
​

Split full_name ("LASTNAME, FIRSTNAME") into separate columns.

Normalize date strings into DATE.

Split category_string into multiple rows for EVENT_LOG.

Day 12 – Load

Load normalized entities into modern.ENTITY_MASTER.

Load exploded events into modern.EVENT_LOG using LegacyID→EntityID mapping.

Weekend (13–14)

Run full pipeline end-to-end: Cloud Storage CSV → Cloud SQL legacy → transform → Cloud SQL modern.

Inspect tables with a client and check a few sample rows.

Week 3 — Automation (Terraform Nuke & Pave)
Day 15 – Terraform variables & outputs

Add variables for regions, instance tier, database names, bucket name.

Output Cloud SQL connection name, bucket name, and VPC info.

Day 16 – Orchestration script

Create scripts/deploy.sh or deploy.ps1 to:

terraform init && terraform apply.

Run generate_data.py and load data into legacy.RECORD_RAW.

Day 17 – Integrate ETL

Extend the script to run migration_engine.py after infra & data load.

Add basic logging (timestamps, step names).

Day 18 – Nuke & Pave

Run terraform destroy to remove Cloud SQL, networking, and Cloud Storage bucket.

Re-run deploy script from scratch and confirm the full pipeline works without manual console changes.

Days 19–21 – Refine

Split Terraform into small files (network.tf, storage.tf, sql.tf).

Add comments and more robust script error handling.

Week 4 — Verification & Documentation
Day 22 – Verification script

Implement verify_migration.py to:

Compare row counts between legacy.RECORD_RAW and modern.ENTITY_MASTER.

Validate expected multiples in EVENT_LOG.

Perform spot checks (e.g., legacy vs modern name for a few IDs).

Day 23 – Run checks & capture logs

Run verification multiple times; log results to docs/verification-log.txt.

Day 24 – Documentation

Write the README.md (below) and confirm it matches your actual steps.

Days 25–29 – Interview story

Practice explaining: legacy GCP migration, Terraform IaC, Cloud SQL design, normalization.

Note trade-offs: instance sizing, free‑tier vs paid, and security (private IP, SSL).

Day 30 – Final run & GitHub

Final Nuke & Pave and clean run.

Push to GitHub as “Project GCP Helix” portfolio project.

README.md for “Project GCP Helix” (Terraform + GCP)
text
# Project GCP Helix: Terraform-Powered Database Migration Simulator

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Duration](https://img.shields.io/badge/Duration-30%20Days-blue)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Platform](https://img.shields.io/badge/Platform-Google%20Cloud%20Free%20Tier-informational)
![Tools](https://img.shields.io/badge/Tools-Python%20%7C%20Terraform%20%7C%20Cloud%20SQL%20%7C%20Cloud%20Storage-informational)

## Executive Summary

**Project GCP Helix** is a self-paced, 30-day hands-on training module inspired by Project Helix, built entirely on Google Cloud. You will migrate records from a legacy, non-normalized flat-file / legacy MySQL schema (the **Source**) to a modern normalized relational schema on Cloud SQL for MySQL (the **Target**), automating the entire pipeline with Python and Terraform.

> **Estimated Time:** ~1.5 hours/day  
> **Theme:** Data Migration, Normalization, and Infrastructure as Code (IaC) on Google Cloud using Terraform

---

## Technical Architecture

| Layer          | Technology / Service                    | Purpose                                                   |
|----------------|-----------------------------------------|-----------------------------------------------------------|
| Infrastructure | Terraform + Google Cloud provider       | Provision VPC, Cloud Storage, Cloud SQL                   |
| Storage        | Cloud Storage                           | Store legacy flat-file CSV input                          |
| Database       | Cloud SQL for MySQL                     | Legacy (`legacy`) and modern (`modern`) schemas           |
| ETL Engine     | Python (Pandas, SQLAlchemy, GCS client) | Extract, transform, and load between schemas              |
| Automation     | Terraform + shell/PowerShell            | Reproducible environment and pipeline execution           |
| Design         | draw.io / diagrams.net                  | ER diagrams (Crow’s Foot notation)                        |

---

## Prerequisites

Before starting **Day 1**, ensure you have:

- A Google Cloud account with Free Tier / Free Trial enabled.  
- A GCP project created and billing enabled.  
- APIs enabled: **Cloud SQL Admin**, **Cloud Storage**, **Compute Engine** (for networking).  
- Terraform installed (v1.3+ recommended).  
- `gcloud` SDK installed and initialized (`gcloud init`).  
- Python 3.10+ with `pip`.  
- MySQL client (WorkBench, DBeaver, or CLI).  
- VS Code or your preferred editor.

### Install Python Dependencies

From the project root:

```bash
pip install pandas sqlalchemy mysql-connector-python google-cloud-storage faker
You may also need to authenticate locally to use the GCS client:

bash
gcloud auth application-default login
Project Structure
bash
Project_GCP_Helix/
├── infrastructure/
│   └── main.tf                # Terraform IaC — VPC, subnets, Cloud Storage, Cloud SQL
├── scripts/
│   ├── generate_data.py       # Generates synthetic legacy flat-file data (CSV + GCS)
│   ├── migration_engine.py    # Core ETL pipeline (Extract → Transform → Load)
│   └── verify_migration.py    # Post-migration data quality checks
├── data/
│   └── legacy_source_data.csv # Auto-generated by generate_data.py
├── docs/
│   └── Architecture_GCP_Migration.png  # ER diagrams (Crow's Foot notation)
└── README.md
Quick Start
Complete these steps to run the full GCP migration pipeline end-to-end.

1. Provision Infrastructure with Terraform
From the infrastructure directory:

bash
terraform init
terraform apply -auto-approve
This provisions:

VPC and subnetwork.

Cloud Storage bucket for legacy CSV.

Cloud SQL MySQL instance (with initial database or blank for later schema creation).

Terraform outputs typically include:

Cloud SQL instance connection name or IP.

Cloud Storage bucket name.

Region/zone details.

2. Generate Legacy Data
From the project root:

bash
python scripts/generate_data.py
This script:

Creates data/legacy_source_data.csv with non-normalized records (similar to a mainframe export).

Uploads the CSV to the Terraform-created Cloud Storage bucket.

3. Load Data into Legacy DB
Using your MySQL client (or a Python helper):

Connect to the Cloud SQL instance.

Create the legacy database and RECORD_RAW table:

sql
CREATE DATABASE IF NOT EXISTS legacy;
USE legacy;

CREATE TABLE IF NOT EXISTS RECORD_RAW (
    raw_id          INT AUTO_INCREMENT PRIMARY KEY,
    full_name       VARCHAR(255),
    dob_string      VARCHAR(50),
    category_string TEXT,
    last_event_date VARCHAR(50)
);
Load the CSV into RECORD_RAW using LOAD DATA LOCAL INFILE or a Python insert script.

4. Run the Migration
bash
python scripts/migration_engine.py
This script:

Connects to the Cloud SQL legacy schema.

Reads legacy.RECORD_RAW into Pandas.

Transforms records to normalized form.

Loads data into modern.ENTITY_MASTER and modern.EVENT_LOG.

Module Breakdown
Week 1 — Foundations
Day 1: Install tools; create Project_GCP_Helix folder structure.

Day 2: Design ER diagrams (legacy vs modern); save to /docs.

Day 3: Author Terraform provider, VPC, and Cloud Storage in main.tf.

Day 4: Add Cloud SQL instance and networking configuration.

Day 5: Implement and run generate_data.py; verify CSV in /data and in Cloud Storage.

Weekend: Test Cloud SQL connectivity; explore data with a MySQL client.

Week 2 — The Migration
Day 8: Create legacy.RECORD_RAW in Cloud SQL and load CSV.

Day 9: Implement migration_engine.py “Connect & Extract” logic.

Day 10: Implement “Transform” (name parsing, date normalization, category explosion).

Day 11: Implement “Load” into modern.ENTITY_MASTER.

Day 12: Implement “Load” into modern.EVENT_LOG, mapping LegacyID to EntityID.

Weekend: Run full migration multiple times and verify results in Cloud SQL.

Week 3 — Automation
Day 15: Parameterize Terraform (variables for region, instance tier, bucket name).

Day 16: Create a deployment script to run Terraform and scripts in sequence.

Day 17: Externalize DB connection settings into config/env variables.

Day 18: “Nuke and Pave” — terraform destroy, then terraform apply + full pipeline rerun.

Days 19–21: Refactor Terraform into modules/files; improve script logging and error handling.

Week 4 — Verification & Documentation
Day 22: Implement verify_migration.py for row counts and spot checks.

Day 23: Run verification; save logs to /docs.

Day 24: Write and refine this README.md.

Days 25–29: Full review, interview-style explanations, and polish.

Day 30: ✅ Final clean Nuke & Pave and successful run, then push to GitHub.

Schema Design
Legacy Source (Flat File / Legacy Schema)
text
RECORD_RAW
──────────────────────────────
raw_id           INT  (PK)
full_name        VARCHAR(255)   ← "LASTNAME, FIRSTNAME" format
dob_string       VARCHAR(50)    ← Mixed formats: YYYYMMDD or YYYY-MM-DD
category_string  TEXT           ← Anti-pattern: "CAT-A1,CAT-B2,CAT-C3"
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
One ENTITY_MASTER record → Many EVENT_LOG records (1:N relationship)

Key Engineering Concepts
Concept	Where Applied
Data Normalization (1NF)	Splitting category_string into rows
Infrastructure as Code	Terraform main.tf provisioning GCP resources
ETL Pipeline Design	migration_engine.py (Extract → Transform → Load)
Date Standardization	Pandas date parsing for mixed string formats
Name Parsing	Splitting "LASTNAME, FIRSTNAME"
Foreign Key Resolution	Mapping LegacyID → EntityID
Data Quality Verification	Row counts, sample checks in verify_migration.py
Reproducibility	Terraform Nuke & Pave cycle
Verification
After running python scripts/verify_migration.py, you should see something like:

text
Legacy Source Rows        : 2000
Modern ENTITY_MASTER Rows : 2000
Modern EVENT_LOG Rows     : 6000
✅ SUCCESS: Record counts and mappings match expectations.

--- Spot Check: Legacy Record ID 1 ---
Legacy  : JOHNSON, MICHAEL
Modern  : FirstName=MICHAEL, LastName=JOHNSON
Differences indicate issues with transform or load logic. Start with:

Name split and trimming.

Date parsing and invalid values.

Category splitting and foreign key joins.

30-Day Calendar
Week	Days	Focus
Week 1	1–5	GCP setup, Terraform infra, data generation
Week 2	8–12	Cloud SQL loading, Python ETL (extract, transform, load)
Week 3	15–21	Terraform automation, Nuke & Pave, refinements
Week 4	22–30	Verification, documentation, interview prep
Contributing
Fork the repository

Create a feature branch (git checkout -b feature/your-improvement)

Commit your changes (git commit -m 'Add improvement')

Push to the branch (git push origin feature/your-improvement)

Open a Pull Request

License
This project is licensed under the MIT License. See the LICENSE file for details.

Built as a hands-on training module for data engineering and cloud migration on Google Cloud, inspired by Project Helix.
