# Project Helix (Azure Edition): Enterprise Data Migration & Modernization

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Cloud](https://img.shields.io/badge/Cloud-Azure-blue)
![Duration](https://img.shields.io/badge/Duration-30%20Days-blueviolet)
![Level](https://img.shields.io/badge/Level-Intermediate-orange)
![Stack](https://img.shields.io/badge/Stack-Python%20%7C%20Terraform%20%7C%20Ansible%20%7C%20Azure%20MySQL-informational)

## Executive Summary

**Project Helix (Azure Edition)** is a 30-day, self-paced training module that simulates a real-world enterprise data migration pipeline on **Microsoft Azure**.
You will migrate records from a legacy, non-normalized flat-file schema (the **Source**) to a modern normalized relational schema (the **Target**) hosted on **Azure Database for MySQL – Flexible Server**, using **Terraform** for Infrastructure as Code and **Python** for ETL.

> **Estimated Time:** ~1.5 hours/day &nbsp;&nbsp;|&nbsp;&nbsp; **Theme:** Data Migration, Normalization, and Cloud Infrastructure as Code (IaC)

---

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Azure & Tool Prerequisites](#azure--tool-prerequisites)
- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [Module Breakdown](#module-breakdown)
  - [Week 1 — Azure Foundations](#week-1--azure-foundations)
  - [Week 2 — Migration in Azure](#week-2--migration-in-azure)
  - [Week 3 — Automation & Rebuild](#week-3--automation--rebuild)
  - [Week 4 — Verification & Portfolio](#week-4--verification--portfolio)
- [Schema Design](#schema-design)
- [Terraform on Azure](#terraform-on-azure)
- [Key Engineering Concepts](#key-engineering-concepts)
- [Verification](#verification)
- [30-Day Calendar](#30-day-calendar)
- [Contributing](#contributing)
- [License](#license)

---

## Architecture Overview

| Layer | Technology | Purpose |
| --- | --- | --- |
| Cloud Provider | Azure Free Account / Sandbox | Host managed MySQL and networking |
| Database | Azure Database for MySQL – Flexible Server | Legacy and modern schemas on a single server |
| Infrastructure as Code | Terraform (azurerm provider) | Provision and tear down Azure resources |
| ETL Engine | Python (Pandas / SQLAlchemy) | Extract, transform, and load data between schemas |
| Automation | Ansible on WSL 2 | Reproducible local workstation setup |
| Design | Visio / draw.io | ER and migration diagrams |

This Azure-focused edition replaces local Docker MySQL containers with a **single Azure MySQL Flexible Server** that hosts both the `legacy_dump` and `enterprise_core` databases, which aligns well with Azure's free-tier options for new accounts.

---

## Azure & Tool Prerequisites

Before starting **Day 1**, you should have:

- A **Microsoft Azure account** (Free, Student, or sandbox tenant).
- **Azure CLI** installed and logged in (`az login`).
- **Terraform** (latest stable) installed.
- **Visual Studio Code** with extensions:
  - Python
  - HashiCorp Terraform
- **Python 3.12+** on Windows.
- **WSL 2 (Ubuntu)** for Ansible.
- **Visio** or **draw.io** for diagrams.

### Python Dependencies

In **Windows PowerShell**:

```powershell
pip install pandas sqlalchemy mysql-connector-python faker
```

---

## Project Structure

```text
Project_Helix_Azure/
├── infrastructure/
│   └── main.tf                     # Terraform for Azure (RG + MySQL Flexible Server + DBs)
├── ansible/
│   ├── hosts.ini                   # Ansible inventory (local)
│   └── setup_environment.yml       # Ansible playbook for workstation dependencies
├── scripts/
│   ├── generate_data.py            # Generates synthetic legacy-style data
│   ├── migration_engine.py         # ETL into Azure MySQL (legacy_dump → enterprise_core)
│   └── verify_migration.py         # Verification and QA checks
├── data/
│   └── legacy_source_data.csv      # Generated source data
├── docs/
│   └── Architecture_Migration_Design_Azure.vsdx  # ER & migration diagrams
└── README_AZURE.md
```

---

## Quick Start

> High-level flow: **Terraform → Generate Data → Load Legacy → Run ETL → Verify**.

### 1. Provision Azure MySQL with Terraform

```bash
cd infrastructure
terraform init
terraform apply -auto-approve
```

This creates:

- A Resource Group.
- An Azure Database for MySQL Flexible Server (Burstable tier, dev-sized).
- Two databases on that server: `legacy_dump` and `enterprise_core`.

> Make sure your IP is allowed to access the server (firewall rules) or that you are using private networking according to your Azure setup.

### 2. Generate Legacy Data Locally

```bash
python scripts/generate_data.py
```

This writes `data/legacy_source_data.csv`, simulating messy legacy flat-file exports.

### 3. Load Data into `legacy_dump` on Azure

Use the Azure MySQL connection string from the portal (or Terraform outputs). Example DSN shape:

```text
<admin_user>@<server-name>.mysql.database.azure.com:3306
```

Create the `RECORD_RAW` table and import the CSV using either MySQL CLI or a small Python loader script.

### 4. Run the Migration ETL

```bash
python scripts/migration_engine.py
```

The ETL will:

1. Extract from `legacy_dump.RECORD_RAW` on Azure.
2. Transform names, dates, and category lists.
3. Load into `enterprise_core.ENTITY_MASTER` and `enterprise_core.EVENT_LOG`.

---

## Module Breakdown

### Week 1 — Azure Foundations

- **Day 1:** Create Azure account; install Azure CLI, Terraform, VS Code; create project structure.
- **Day 2:** Design ER diagrams (legacy vs modern) and save to `/docs`.
- **Day 3:** Write `main.tf` for Azure Resource Group and MySQL Flexible Server.
- **Day 4:** Run `terraform apply` and verify MySQL server + DBs exist.
- **Day 5:** Implement and run `generate_data.py`; inspect `legacy_source_data.csv`.
- **Weekend:** Explore Azure portal for MySQL Flexible Server (metrics, connection strings).

### Week 2 — Migration in Azure

- **Day 8:** Create `RECORD_RAW` in `legacy_dump` and import CSV to Azure MySQL.
- **Day 9:** Implement ETL connection logic in `migration_engine.py` targeting Azure.
- **Day 10:** Add transform logic (name parsing, date normalization).
- **Day 11:** Load `ENTITY_MASTER` into `enterprise_core`.
- **Day 12:** Load `EVENT_LOG` by exploding comma-separated category lists.
- **Weekend:** Query Azure MySQL directly to validate schema and sample rows.

### Week 3 — Automation & Rebuild

- **Day 15:** Install Ansible in WSL (`sudo apt install ansible -y`).
- **Day 16:** Create `hosts.ini` and `setup_environment.yml` for local environment automation.
- **Day 17:** Run Ansible playbook; confirm all Python dependencies are present.
- **Day 18:** "Nuke and Pave" — `terraform destroy`, `terraform apply`, rerun ETL end-to-end.
- **Days 19–21:** Refine scripts, improve logging, and add in-code documentation.

### Week 4 — Verification & Portfolio

- **Day 22:** Implement `verify_migration.py` to compare source/target and sample a record.
- **Day 23:** Run verification; save logs to `/docs`.
- **Day 24:** Write or update `README_AZURE.md` to reflect the cloud architecture.
- **Days 25–29:** Practice explaining the Azure architecture and pipeline as if in an interview.
- **Day 30:** Run `terraform destroy` to clean up resources and finalize your portfolio artifact.

---

## Schema Design

### Legacy Source (Flat File — Non-Normalized)

```text
RECORD_RAW
────────────────────────────────────
raw_id           INT  (PK)
full_name        VARCHAR(255)   ← "LASTNAME, FIRSTNAME" format
dob_string       VARCHAR(50)    ← Mixed: YYYYMMDD or YYYY-MM-DD
category_string  TEXT           ← "CAT-A1,CAT-B2,CAT-C3" (multi-value)
last_event_date  VARCHAR(50)
```

### Modern Target (Azure MySQL — Normalized)

```text
ENTITY_MASTER                          EVENT_LOG
──────────────────────────             ─────────────────────────────
EntityID   INT  (PK)  ◄───────────┐   EventID      INT  (PK)
FirstName  VARCHAR                  └─ EntityID    INT  (FK)
LastName   VARCHAR                     CategoryCode VARCHAR
DOB        DATE                        EventDate    DATE
LegacyID   INT  (traceability)
```

One `ENTITY_MASTER` record can be related to many `EVENT_LOG` records, resolving the multi-value `category_string` anti-pattern.

---

## Terraform on Azure

The `infrastructure/main.tf` file is responsible for:

- Declaring the **Azure provider**.
- Creating a **Resource Group**.
- Creating an **Azure Database for MySQL Flexible Server** (development-sized, suitable for free-tier testing).
- Creating logical databases `legacy_dump` and `enterprise_core` on that server.

This lets you:

- Rebuild your Azure infrastructure consistently with `terraform apply`.
- Tear everything down with `terraform destroy` to avoid surprise costs.

> For production, you would harden security (VNet integration, private endpoints, stronger auth), but those are intentionally simplified here for training.

---

## Key Engineering Concepts

| Concept | Where It Appears |
| --- | --- |
| Data Normalization (1NF) | Splitting `category_string` into `EVENT_LOG` rows |
| Cloud Database Design | Azure Database for MySQL hosting multiple schemas |
| Infrastructure as Code | Terraform definitions for Azure resources |
| Configuration Management | Ansible playbook for workstation dependencies |
| ETL Pipeline | `migration_engine.py` (Extract → Transform → Load) |
| Date Standardization | `pd.to_datetime` handling mixed legacy formats |
| Name Parsing | Splitting "LASTNAME, FIRSTNAME" into separate columns |
| Data Quality Checks | `verify_migration.py` comparing counts and samples |
| Cost & Lifecycle Management | Using `terraform destroy` to clean up cloud resources |

---

## Verification

After running your pipeline in Azure, `verify_migration.py` should show matching row counts between:

- `legacy_dump.RECORD_RAW`
- `enterprise_core.ENTITY_MASTER`

Example expected output shape (values will differ):

```text
Legacy Rows (RECORD_RAW)     : 2000
Modern Rows (ENTITY_MASTER)  : 2000
SUCCESS: Record counts match.

--- Spot Check: LegacyID = 1 ---
Legacy  : JOHNSON, MICHAEL
Modern  : JOHNSON, MICHAEL
```

Any mismatch suggests a transform or load issue; start by checking your name-splitting logic and the join on `LegacyID`.

---

## 30-Day Calendar

| Week | Days | Focus |
| --- | --- | --- |
| **Week 1** | 1–5 | Azure setup, ER design, Terraform for MySQL, data generation |
| **Week 2** | 8–12 | Loading into Azure, ETL pipeline targeting Azure MySQL |
| **Week 3** | 15–21 | Ansible automation, full "Nuke and Pave" rebuild |
| **Week 4** | 22–30 | Verification, documentation, interview prep, teardown |

> The "Nuke and Pave" exercise (Day 18) is your proof that the environment is fully reproducible — a key expectation for cloud data engineers and DevOps practitioners.

---

## Contributing

Contributions are welcome to improve the exercises, documentation, or automation.

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/azure-improvements`).
3. Commit your changes (`git commit -m "Improve Azure training flow"`).
4. Push the branch (`git push origin feature/azure-improvements`).
5. Open a Pull Request.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

*Project Helix (Azure Edition) is designed as a hands-on, interview-ready portfolio project for aspiring data engineers and cloud-focused DevOps practitioners.*
