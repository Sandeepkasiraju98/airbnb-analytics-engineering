# 🏠 Airbnb Analytics Engineering Pipeline

> End-to-end data pipeline built on Snowflake, dbt, and AWS, transforming raw Airbnb data into analytics-ready insights through a medallion architecture.

---

## What This Does
Raw CSVs go in. Business intelligence comes out. In between, a fully automated pipeline cleans, validates, and models Airbnb bookings, listings, and host data across three layers — Bronze, Silver, and Gold, with historical tracking via SCD Type 2 snapshots.

---

## Stack
| Layer | Tool |
|---|---|
| Cloud Warehouse | Snowflake |
| Transformation | dbt |
| Storage | AWS S3 |
| Language | Python 3.12+ / SQL / Jinja |

---

## Architecture
```
CSV → S3 → Snowflake Staging → Bronze → Silver → Gold
                                  ↓         ↓       ↓
                               Raw       Cleaned  Analytics
```

**Bronze** — raw ingestion, minimal touch  
**Silver** — validated, standardized, enriched  
**Gold** — denormalized OBT + fact tables, ready for BI  

---

## Key Engineering Decisions

**Incremental Loading** — models process only new records, not full refreshes, keeping compute costs low

**Dynamic SQL via Jinja** — joins and column selection driven by config arrays, making the OBT model maintainable and extensible without rewriting SQL

**Custom Macros** — reusable logic for price tagging, string trimming, and schema naming baked into the dbt layer

**SCD Type 2 Snapshots** — full historical tracking on bookings, listings, and hosts with valid-from/to dates maintained automatically

---

## Project Structure
```
├── SourceData/          # Raw CSVs
├── DDL/                 # Schema definitions
└── airbnb_pulse/        # dbt project
    ├── models/
    │   ├── bronze/
    │   ├── silver/
    │   └── gold/
    ├── snapshots/        # SCD Type 2
    ├── macros/           # Reusable logic
    └── tests/            # Data quality
```

---

## Running It

```bash
# Setup
dbt debug          # verify connection
dbt deps           # install packages

# Execute
dbt run            # run all models
dbt snapshot       # update SCD tables
dbt test           # validate data quality

# Docs
dbt docs generate && dbt docs serve
```

---

## Data Quality
- Not null + uniqueness tests on all key columns
- Custom SQL tests for business rule validation
- Full lineage tracking — from source CSV to gold table — visible in dbt docs

---

## Setup
1. Clone the repo
2. Configure `~/.dbt/profiles.yml` with your Snowflake credentials
3. Run `DDL/ddl.sql` in Snowflake to scaffold the database
4. Load CSVs → `AIRBNB.STAGING.*`
5. `dbt build` — runs everything end to end

> ⚠️ Never commit `profiles.yml`. Use environment variables for credentials.

---

## Roadmap
- CI/CD pipeline integration
- BI tool connection (Tableau / Power BI)
- PII masking layer
- Alerting and monitoring

---

**Built as a portfolio demonstration of modern analytics engineering practices.**
