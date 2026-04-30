# 🏛️ Modern Data Warehouse — SQL Server Medallion Architecture

A modern, scalable data warehouse built on **Microsoft SQL Server**, implementing the **Medallion Architecture** pattern to progressively refine raw data into trusted, analytics-ready datasets across three structured layers.

---

## 📐 Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                      DATA SOURCES                               │
│         (APIs · Flat Files · Databases · Streams)               │
└───────────────────────────┬─────────────────────────────────────┘
                            │  Ingestion
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  🟫  BRONZE LAYER  (Raw / Landing Zone)                         │
│  ─────────────────────────────────────────────────────────────  │
│  • Exact copy of source data — no transformations               │
│  • Append-only / insert-only tables                             │
│  • Schema: [bronze].*                                           │
└───────────────────────────┬─────────────────────────────────────┘
                            │  Cleanse & Standardise
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  🩶  SILVER LAYER  (Cleansed / Conformed)                       │
│  ─────────────────────────────────────────────────────────────  │
│  • Deduplicated, validated, type-cast data                      │
│  • Business rules & referential integrity applied               │
│  • Schema: [silver].*                                           │
└───────────────────────────┬─────────────────────────────────────┘
                            │  Aggregate & Enrich
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  🥇  GOLD LAYER  (Business / Analytics-Ready)                   │
│  ─────────────────────────────────────────────────────────────  │
│  • Star / snowflake schema — facts & dimensions                 │
│  • KPIs, aggregates, and domain-specific data marts             │
│  • Schema: [gold].*                                             │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
              📊  BI Tools · Reports · ML Models
```

---

## 📁 Project Structure

```
data-warehouse/
│
├── datasets/                   # Raw source data files (CSV, JSON, etc.)
│
├── docs/                       # Architecture diagrams & data dictionaries
│   ├── data_catalog.md
│   └── architecture_diagram.png
│
├── scripts/
│   ├── init/                   # Database & schema initialisation
│   │   ├── 00_create_database.sql
│   │   └── 01_create_schemas.sql
│   │
│   ├── bronze/                 # Raw ingestion stored procedures & DDL
│   │   ├── ddl_bronze_tables.sql
│   │   └── proc_load_bronze.sql
│   │
│   ├── silver/                 # Cleansing & transformation logic
│   │   ├── ddl_silver_tables.sql
│   │   └── proc_load_silver.sql
│   │
│   └── gold/                   # Dimensional model & aggregation views
│       ├── ddl_gold_views.sql
│       └── proc_load_gold.sql
│
└── README.md
```

---

## 🥉🥈🥇 Layer Definitions

### 🟫 Bronze — Raw Layer
The landing zone for all source data. Data is ingested **as-is** with no transformations applied, preserving full fidelity for auditing and reprocessing.

| Property | Detail |
|---|---|
| Schema | `[bronze]` |
| Load strategy | Full load / incremental append |
| Transformations | None |
| Data quality | Unvalidated |
| Retention | Full history |

### 🩶 Silver — Cleansed Layer
Data is standardised, deduplicated, and validated. Business rules are applied to produce a reliable, conformed data store.

| Property | Detail |
|---|---|
| Schema | `[silver]` |
| Load strategy | Upsert (MERGE) |
| Transformations | Type casting, null handling, deduplication |
| Data quality | Validated & conformed |
| Retention | Full history |

### 🥇 Gold — Analytical Layer
Business-ready data modelled as facts and dimensions (star schema). Serves as the single source of truth for BI tools, dashboards, and downstream consumers.

| Property | Detail |
|---|---|
| Schema | `[gold]` |
| Load strategy | Rebuild / incremental |
| Transformations | Aggregation, KPI calculation, enrichment |
| Data quality | Curated & trusted |
| Retention | Current state + history where needed |

---

## 🚀 Getting Started

### Prerequisites
- Microsoft SQL Server 2019+ (or Azure SQL Database)
- SQL Server Management Studio (SSMS) or Azure Data Studio
- Sufficient permissions to create databases, schemas, and stored procedures

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-org>/<your-repo>.git
   cd <your-repo>
   ```

2. **Initialise the database and schemas**
   ```sql
   -- Run in order
   EXEC scripts/init/00_create_database.sql
   EXEC scripts/init/01_create_schemas.sql
   ```

3. **Create layer objects**
   ```sql
   EXEC scripts/bronze/ddl_bronze_tables.sql
   EXEC scripts/silver/ddl_silver_tables.sql
   EXEC scripts/gold/ddl_gold_views.sql
   ```

4. **Load data**
   ```sql
   EXEC bronze.load_bronze;   -- Ingest raw source data
   EXEC silver.load_silver;   -- Cleanse & conform
   EXEC gold.load_gold;       -- Build analytical layer
   ```

---

## 🔄 Data Flow & Load Strategy

```
Source Systems
     │
     ▼  (Full / Incremental Extract)
[bronze] Tables          ← Append-only, timestamped
     │
     ▼  (MERGE / UPSERT)
[silver] Tables          ← Deduplicated, validated
     │
     ▼  (Rebuild / MERGE)
[gold] Views / Tables    ← Star schema, aggregated
```

---

## 🛡️ Design Principles

- **Immutability at Bronze** — Raw data is never modified after landing
- **Single Source of Truth** — Silver is the authoritative cleansed layer
- **Separation of Concerns** — Each layer has a single, well-defined responsibility
- **Reprocessability** — Any layer can be rebuilt by replaying from Bronze
- **Auditability** — Load timestamps and source metadata tracked throughout

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'feat: add your feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

Please follow the existing naming conventions for SQL objects and scripts.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

> **Built with SQL Server · Medallion Architecture · Modern Data Engineering practices**
