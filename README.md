# fabric-ecom-supplychain-analytics

> End-to-end supply-chain analytics on Microsoft Fabric from raw CSVs to Power BI dashboards. Includes Lakehouse transformations, warehouse upserts, a semantic model with DAX and RLS roles, and rich reporting—all version-controlled via GitHub.

## Description

This repository contains all artifacts and code for an end-to-end supply-chain analytics solution on Microsoft Fabric, built entirely from a single raw CSV file. The project demonstrates how to transform flat ecommerce order data into a fully normalized snowflake schema with fact and dimension tables using PySpark notebooks and T-SQL in Fabric.

Fabric notebooks cleanse and enrich the raw data, while a stored-procedure-driven transformation loads structured data into Warehouse tables. A robust semantic model defines business logic and KPIs, and Power BI delivers rich, interactive supply-chain reports. Row-Level Security (RLS) roles have been implemented via Tabular Editor.

## Key Features

- **Automated CSV Ingestion**: A pipeline ingests daily supplier CSV files, compares file timestamps using metadata, and processes only new files.
- **Lakehouse Bronze & Silver Layers**:
  - Bronze: Raw CSV files land in OneLake `/files/`.
  - Silver: Notebooks clean and write structured data into Delta tables (e.g. `DimCustomers`, `FactOrders`).
- **Warehouse DDL & Upsert**:
  - Staging views point to Lakehouse Silver tables.
  - A stored procedure merges new/changed data into Gold fact/dimension tables.
- **Semantic Modeling**:
  - Fabric semantic model defines relationships, DAX measures, and hierarchies.
  - RLS roles (`Corporate_Director`, `Home_Office_Director`, `Consumer_Director`) restrict access by customer segment.
  - Best practices applied using Tabular Editor.
- **Power BI Reporting**:
  - Interactive reports and dashboards visualize sales, customer, and product metrics.
  - Executives can monitor KPIs, late shipments, and demand trends.
- **GitHub Version Control**:
  - All development artifacts are version-controlled in this public GitHub repository for full traceability.

## Folder Structure

```
fabric-artifacts/
  data_engineering/
    lakehouse/                  # Lakehouse JSON definitions
    notebooks/                  # Transformation notebooks (.ipynb)
  data_warehousing/
    warehouse/                  # DDL: schema.sql, tables/*.sql, views/*.sql
    semantic_model/             # Semantic model metadata (.bim/.json)
  power_bi/
    reports/                    # Power BI .pbix/.pbit files
    dashboards/                 # Power BI dashboard JSON exports
```

## Snowflake Schema Overview

The following tables were created from the single source CSV:

### Dimensions
- `DimCategories`
- `DimCountries`
- `DimCustomers`
- `DimDates`
- `DimDepartments`
- `DimProducts`

### Fact Table
- `FactOrders`

### Supporting Table
- `IngestionLogs` (for monitoring pipeline status)

## Getting Started

Follow these steps to set up your local environment and connect to the Fabric Dev workspace.

1. Clone the Repository
```bash
git clone https://github.com/yourorg/ecom-supplychain-analytics-fabric.git
cd ecom-supplychain-analytics-fabric
```

2. Install the Fabric CLI (Optional)
To sync artifacts locally or inspect changes:
```bash
pip install --upgrade ms-fabric-cli
```

3. Configure Dev Git Integration
    1. Open the **Dev** workspace in the Fabric portal.  
    2. Navigate to **Settings → Git integration → Connect**.  
    3. Select your GitHub repo and branch (`main`), and set the path to `fabric-artifacts/`.  
    4. Enable **Auto-sync on push** and click **Publish**.

4. Verify Artifacts
After publishing, locally run:
```bash
git pull origin main
```
You should see the `fabric-artifacts/` folder populated with subdirectories for data engineering, warehousing, and Power BI.

## Prerequisites

Before you begin, ensure you have the following:

- **Microsoft Fabric Tenancy**  
  - Dev workspace provisioned in your region.

- **GitHub Repository**  
  - A repo named `ecom-supplychain-analytics-fabric` with a single branch (`main`).

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository on GitHub.
2. Create a feature branch off `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. Add or update artifacts under `fabric-artifacts/` (Lakehouse JSON, notebooks, warehouse SQL, semantic model, or Power BI files).
4. Commit your changes:
   ```bash
   git add -A
   git commit -m "feat: describe your change"
   ```
5. Push to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```
6. Open a Pull Request against the upstream `main` branch.
7. Review & Merge:
   - CI will validate your changes; once checks pass, a reviewer can merge.
