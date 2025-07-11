# Supply Chain Project

Open-source Supply Chain analytics on Microsoft Fabric: scalable medallion architecture (Bronze-Silver-Gold), automated CSV ingestion, Delta Lake transformations, semantic modeling with DAX & RLS, and interactive Power BI reports. Contribute to refine pipelines, models, and dashboards.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Setup and Installation](#setup-and-installation)
- [Repository Structure](#repository-structure)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project follows the medallion architecture which is a multi-tiered approach where each zone progressively refines data quality and readiness:

- **Bronze**: Raw landing zone for unprocessed CSVs.
- **Silver**: Cleansed and enriched data, standardized and validated. A single PySpark notebook is used to perform scalable transformation and enrichment in this layer.
- **Gold**: Consumption-ready, high-value fact and dimension tables, powering semantic models and reporting.

The Supply Chain Project demonstrates a medallion-based analytics solution on Microsoft Fabric:

- **Bronze (Raw Landing Zone)**: Pipeline performs metadata-driven filtering and timestamp comparison using `Lookup`, `GetMetadata`, and `Filter` activities to ingest only new or changed raw files.
- **Silver (Cleansed & Enriched)**: Notebooks transform copied data into Delta format, including cleansing, deduplication, and enrichment.
- **Gold (Analytics & Reporting)**: SQL scripts create or refresh staging views that mirror Lakehouse Delta tables, and a stored procedure performs UPSERT logic into the Data Warehouse.
- **Semantic Model**: A Power BI semantic model is automatically refreshed at the end of the pipeline using the `PBISemanticModelRefresh` activity.
- **Power BI Reporting**: Final `.pbix` reports provide interactive dashboards sourced from the refreshed semantic model.

## Prerequisites

- [Microsoft Fabric](https://learn.microsoft.com/fabric/) workspace
- Azure storage account for lakehouse
- Fabric Data Warehouse endpoint
- Power BI Desktop (for `.pbix` files)

## Setup and Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-org/SupplyChain.git
   cd SupplyChain/fabric-artifacts
   ```

2. **Configure environment variables**
   - Populate `.env` in `core/` and `orchestration/` with Fabric credentials and connection strings.

3. **Deploy Data Layers**
   ```bash
   cd core/LH_SupplyChain && python deploy_lakehouse.py
   cd ../WH_SupplyChain && python deploy_warehouse.py
   cd ../SM_SupplyChain && tabular-editor deploy
   ```

4. **Run Pipelines**
   ```bash
   cd ../../orchestration/PL_SupplyChain
   az datafactory pipeline create-run \
     --factory-name MyFabricFactory \
     --resource-group MyRG \
     --pipeline-name LoadSupplyChainData
   ```
   This pipeline handles:
   - **Incremental ingestion** of targeted files based on modification timestamp
   - **Notebook-driven transformation** into Delta Lake tables
   - **Warehouse upserts** via SQL stored procedures
   - **View generation** for staging tables
   - **Semantic model refresh** via Power BI REST API
   - **Logging** into the `IngestionLogs` table for traceability

5. **Launch Notebooks**
   ```bash
   cd ../NB_SupplyChain
   jupyter lab
   ```

6. **Open Power BI Report**
   - Open `delivery/SupplyChain/SupplyChainReport.pbix` in Power BI Desktop and verify that the semantic model has been refreshed.

## Repository Structure

```plaintext
fabric-artifacts/
├── core/
│   ├── LH_SupplyChain          # Lakehouse JSON definitions (Bronze & Silver zones)
│   ├── WH_SupplyChain          # Warehouse DDL and upsert scripts (Gold zone)
│   └── SM_SupplyChain          # Semantic model definitions and refresh settings
├── orchestration/
│   ├── PL_SupplyChain          # Pipelines including ingestion, transforms, semantic refresh
│   └── NB_SupplyChain          # Jupyter notebooks for cleansing, enrichment, profiling
└── delivery/
    └── SupplyChain             # Power BI `.pbix` report and related assets
```

## Usage

1. **Ingest & Land**: Trigger `PL_SupplyChain` pipeline to pull new or updated CSVs into Bronze using file metadata.
2. **Transform & Enrich**: Notebooks in `NB_SupplyChain` standardize and produce Silver Delta tables.
3. **Load to Warehouse**: Pipeline executes `CREATE VIEW` and stored procedures to upsert Silver into Gold tables.
4. **Refresh Semantic Model**: The pipeline triggers a Power BI dataset refresh automatically.
5. **View Reports**: Open the Power BI report (`delivery/SupplyChain/SupplyChainReport.pbix`) which uses the refreshed semantic layer.

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature/YourFeature
   ```
3. Add or update artifacts under `fabric-artifacts/`.
4. Commit your changes:
   ```bash
   git add -A
   git commit -m "feat: Describe your change"
   ```
5. Push and open a Pull Request.

## License

This project is licensed under the MIT License 