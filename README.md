
# CareSync — Azure Data Engineering Project
## Azure Data Factory (ADF) Repository

![Azure](https://img.shields.io/badge/Azure-Data%20Factory-0078D4?logo=microsoftazure)
![Data Engineering](https://img.shields.io/badge/Domain-Data%20Engineering-blue)
![Status](https://img.shields.io/badge/Project-In%20Progress-orange)

## 1. Project Overview

CareSync is an end-to-end Azure Data Engineering project designed to demonstrate how data can be ingested, organized, transformed, validated, and prepared for analytical consumption using Azure cloud services.

This repository contains the Azure Data Factory implementation used to orchestrate data movement and coordinate processing across the data pipeline.

**Repository:**  
https://github.com/Umashankar0257/caresync_adf_repo

**Databricks Repository:**  
https://github.com/Umashankar0257/caresync-adb-repo

## 2. Business Objective

Healthcare-related data can originate from multiple source systems and arrive in different formats.

The objective of this project is to build a reusable data pipeline that can:

- Ingest data into an Azure Data Lake.
- Organize incoming data using a consistent folder structure.
- Pass metadata and parameters between pipeline activities.
- Integrate Azure Data Factory with Azure Databricks.
- Support downstream data transformation and validation.
- Prepare curated datasets for reporting and analytics.

The project uses a healthcare-inspired CareSync scenario for learning and demonstrating Azure Data Engineering concepts.

## 3. Technology Stack

| Technology | Purpose |
|---|---|
| Azure Data Factory | Pipeline orchestration and data movement |
| Azure Data Lake Storage Gen2 | Cloud data storage |
| Azure Databricks | Distributed data processing |
| Apache Spark / PySpark | Data transformation |
| Delta Lake | Reliable table storage for supported Delta datasets |
| Unity Catalog Volumes | File access and organization in Databricks |
| GitHub | Source control and project versioning |
| Git | Branching and collaborative development |

## 4. High-Level Architecture

```text
Source Data
    |
    v
Azure Data Factory
    |
    |-- Pipeline parameters
    |-- Datasets
    |-- Linked services
    |-- Data movement / orchestration
    |
    v
Azure Data Lake Storage Gen2
    |
    v
Databricks Processing
    |
    |-- Landing to Bronze
    |-- Bronze to Silver
    |-- Data cleansing and validation
    |-- Gold transformations
    |
    v
Curated Analytical Data
```

> The diagram describes the target end-to-end workflow. Actual activities and integrations depend on the deployed pipeline configuration.

## 5. Repository Structure

```text
caresync_adf_repo/
│
├── dataset/
│   └── ADF dataset definitions
│
├── factory/
│   └── Data Factory configuration
│
├── linkedService/
│   └── Connection definitions
│
├── pipeline/
│   └── Pipeline definitions
│
├── trigger/
│   └── Trigger definitions
│
├── publish_config.json
└── README.md
```

### What Each Folder Does

**dataset/**  
Contains dataset definitions that describe the structure, location, and format of data used by pipeline activities.

**factory/**  
Contains Azure Data Factory factory configuration metadata.

**linkedService/**  
Defines connection information for supported source systems, storage accounts, and compute services. Credentials should be managed securely.

**pipeline/**  
Contains the pipeline JSON definitions describing activities, dependencies, parameters, and execution flow.

**trigger/**  
Contains trigger definitions used to initiate pipelines when the corresponding triggers are configured and enabled.

**publish_config.json**  
Contains project-specific publishing configuration.

## 6. How the Pipeline Works

### Step 1 — Source Data

Data is supplied by the configured source systems or files.

The pipeline must identify the source location, data format, and required ingestion parameters.

### Step 2 — Pipeline Parameters

Parameters allow the same pipeline to be reused for different source systems, tables, or data paths without duplicating the complete pipeline.

Example parameters:

```text
source_system
table_name
landing_timestamp
```

Their actual names and values must match the deployed pipeline definitions.

### Step 3 — Linked Services

Linked services define how Azure Data Factory connects to supported resources.

Examples of possible connections include Azure Data Lake Storage Gen2 and Azure Databricks.

Authentication should use managed identity, service principals, or another approved secure method where applicable.

### Step 4 — Datasets

Datasets describe the data location and format that an activity reads from or writes to.

Parameterized datasets can make folder paths and file names reusable across multiple ingestion scenarios.

### Step 5 — Data Movement

When configured, a Copy activity can read data from a source and write it to a destination.

Its source, sink, mappings, and format settings determine how data is moved.

Lookup activities can retrieve configuration or metadata to drive subsequent activities. A Lookup activity is not itself a replacement for copying data.

### Step 6 — Databricks Integration

ADF can coordinate Databricks notebook execution by passing parameters to a notebook activity.

The notebook then performs the processing defined in the Databricks repository.

### Step 7 — Monitoring

Pipeline runs should be monitored for:

- Successful and failed activities.
- Execution duration.
- Input and output details where available.
- Parameter and configuration errors.
- Authentication and connectivity failures.

## 7. Parameterization Strategy

Parameterization improves reuse and reduces hard-coded paths.

For example, a configurable ingestion path might follow this pattern:

```text
<container>/<source_system>/<table_name>/<landing_timestamp>/
```

Illustrative example:

```text
landing/appointments/appointments/2026-10-10T10-00-00/
```

The example represents a possible folder naming convention, not a claim that this exact folder exists in the deployed environment.

Typical benefits include:

- Reusing pipelines for multiple tables.
- Reducing duplicate pipeline definitions.
- Making configuration changes easier.
- Supporting metadata-driven ingestion patterns.

## 8. Orchestration and Dependencies

ADF pipelines can coordinate activities through dependency conditions.

A typical workflow is:

1. Validate configuration.
2. Ingest source data.
3. Confirm successful completion of ingestion.
4. Invoke the required Databricks notebook.
5. Capture processing status.
6. Handle failures and notify the appropriate support channel, if configured.

Each downstream activity should execute only when its required upstream activity has completed successfully.

## 9. Error Handling and Reliability

Recommended production practices include:

- Configure activity retry policies where appropriate.
- Capture pipeline run identifiers.
- Log failures with useful diagnostic information.
- Validate source and destination paths.
- Avoid exposing passwords, access tokens, or connection strings in Git.
- Make ingestion safe to rerun where possible.
- Monitor failed pipeline runs and investigate root causes.

Retry policies should be selected carefully to avoid duplicate writes or repeated side effects.

## 10. GitHub Version Control

The repository stores exported Azure Data Factory definitions in Git.

A team-based workflow can be:

```text
main
  |
  └── feature/ingestion-enhancement
             |
             ├── Update pipeline JSON
             ├── Validate changes
             ├── Commit and push
             ├── Open Pull Request
             ├── Review by manager
             └── Merge after approval
```

After merging, the developer synchronizes the latest `main` branch before starting another feature.

Git version control and Azure deployment are separate concerns. A successful merge does not, by itself, prove that the pipeline has been deployed to the target Azure environment.

## 11. Deployment Considerations

Before running the pipeline in another environment:

1. Configure the required Azure resources.
2. Verify linked services and authentication.
3. Confirm dataset paths and pipeline parameters.
4. Configure the correct Databricks workspace and notebook references.
5. Validate trigger configuration.
6. Test the pipeline with sample data.
7. Review monitoring and failure handling.
8. Publish or deploy the definitions using the appropriate environment workflow.

Do not commit credentials or environment-specific secrets.

## 12. Current Scope and Future Enhancements

This repository is part of an evolving learning project.

Potential enhancements include:

- Metadata-driven ingestion for additional tables.
- Stronger pipeline validation.
- Centralized logging and monitoring.
- Improved retry and recovery mechanisms.
- Environment-specific deployment configuration.
- Automated validation through CI/CD.
- Additional source connectors and ingestion patterns.

Only mark an enhancement as implemented after validating the corresponding pipeline definition and deployed configuration.

## 13. Related Repository

The companion repository contains the Databricks notebooks used for data processing:

https://github.com/Umashankar0257/caresync-adb-repo

Refer to its README for landing, Bronze, Silver, and Gold processing details.

## 14. Key Learning Outcomes

- Azure Data Factory pipeline orchestration.
- Linked services and dataset configuration.
- Pipeline parameterization.
- Source-to-destination data movement concepts.
- Integration between ADF and Databricks.
- Git-based collaboration and pull requests.
- Pipeline monitoring, debugging, and deployment concepts.

## 15. Author

**Umashankar Reddy M**

Azure Data Engineering | Azure Data Factory | Azure Databricks | PySpark | SQL | Delta Lake | GitHub

---

**Note:** This is a portfolio and learning project. Resource names, connections, execution schedules, and deployment settings should be verified against the actual Azure environment before production use.
