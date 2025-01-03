# Solution Architecture for Healthcare Revenue Cycle Management (RCM)


![image](https://github.com/user-attachments/assets/e6f82260-ccde-4b3b-a3fe-e14dcbdbedda)


This project implements a comprehensive data engineering pipeline for **Healthcare Revenue Cycle Management (RCM)** using the **Medallion Architecture** in **Azure**. The architecture is divided into **Landing**, **Bronze, Silver, and Gold layers**, each serving a specific role in transforming raw data into actionable insights.

## Medallion Architecture

The solution leverages the **Medallion Architecture**, comprising three key layers: **Landing**, **Bronze**, **Silver**, and **Gold**, each with distinct responsibilities.
### 1. Landing Layer

- **Purpose**: Serves as the entry point exclusively for claims data in CSV format, uploaded by insurance providers.
- **Data Format**: CSV files
- **Key Characteristics**: Immutable, raw data; no processing or transformation applied.

### 2. Bronze Layer (Source of Truth)

- **Purpose**: The Bronze layer serves as the initial zone for raw data from EMR (Azure SQL DB) and NPI, ICD, and CPT (public API). It stores data in its native format with minimal transformations, ensuring immutability and serving as the single source of truth for downstream processes.
  
- **Data Sources**:
    - **EMR Data**: Ingested from Azure SQL DB in Parquet format.
    - **NPI and ICD Codes**: Extracted from public APIs and stored in Parquet format.
    - **CPT Code Data**: Obtained from a public API in Parquet format.

- **Data Format**: All datasets in the Bronze layer are stored in **Parquet** format, which offers efficient storage and optimal query performance for large datasets.

- **Key Characteristics**:
    - Data is **immutable**, preserving the original raw format.
    - Acts as the **single source of truth** for all downstream processes.

### 3. Silver Layer (Clean and Enriched Data)

- **Purpose**: The Silver layer contains cleaned, enriched, and standardized data ready for analysis and reporting.
  
- **Key Processes**:
    - **Data Cleaning**: Null values are handled, data quality checks are applied, and bad records are quarantined.
    - **Common Data Model (CDM)**: Standardises data from different sources to resolve schema discrepancies and potential ID clashes (e.g., patient tables from different hospitals).
    - **Slowly Changing Dimension (SCD) Type 2**: Maintains historical records, ensuring that changes in patient demographics or other data are properly tracked.
    - **Data Storage in Delta Tables**: Transformed data is stored in Delta tables in the silver layer. Delta tables, built on top of Parquet and incorporated additional logs, provide ACID transactions, support for data updates, efficient incremental loads, and versioned data history.

### 4. Gold Layer (Facts and Dimensions)

- **Purpose**: The Gold layer contains curated, aggregated data modelled into fact and dimension tables for business reporting and KPI generation.

- **Key Processes**:
    - Creation of **fact tables**, such as `fact_transaction`, to capture key business events.
    - Development of **dimension tables**, like `dim_patient`, `dim_provider`, and `dim_department`, to provide context and details for the facts.

- **Data Format**: Data is stored in **Delta tables**, optimised for reporting and analytical queries.

## Data Flow

1. **Landing Layer**: Claims data uploaded by insurance providers in CSV format.
2. **Bronze Layer**: Data from various sources (EMR databases, APIs) is ingested in **Parquet** format.
3. **Silver Layer**: Data is cleansed, standardized, and enriched using techniques like data quality checks, CDM implementation, and SCD Type 2. Data is stored in **Delta tables**.
4. **Gold Layer**: Data from the Silver layer is transformed into fact and dimension tables, also stored as **Delta tables** for optimized reporting.

## Key Technologies

- **Azure Data Factory**: Orchestrates data movement and pipeline execution, including invoking Databricks notebooks for data transformations.
- **Azure Databricks**: Provides a platform for data processing and transformation using **Python** and **Apache Spark**. Databricks notebooks are used to implement data cleaning, CDM, SCD Type 2, and the creation of fact and dimension tables.
- **Azure SQL DB**: Stores the initial **EMR data** for the two hospitals.
- **Azure Data Lake Storage (ADLS) Gen 2**: Serves as the primary storage for raw and processed data across all layers (Landing, Bronze, Silver, and Gold).
- **Azure Key Vault**: Securely stores sensitive credentials, like access keys and API tokens, to enhance security and prevent hardcoding.
- **Unity Catalog**: Enables centralised metadata management for the data lake, improving discoverability, data lineage, and collaboration.

## Best Practices

- **Metadata-driven pipeline design**: A configuration file defines data sources and pipeline parameters, enhancing reusability and maintainability.
- **Parallel pipeline execution**: The initial sequential data processing was slow due to the auto-incrementing key in the audit table. By removing the auto-incrementing key and enabling parallel processing in Azure Data Factory, the pipeline now runs much faster, significantly improving efficiency.
- **Implementing Azure Key Vault**:  Securely stores and accesses sensitive information such as passwords and access tokens and eliminates hardcoding of credentials in code, aligning with industry best practices.
- **Utilising Unity Catalog for Metadata Storage**: Replaces the local Hive metastore with Unity Catalog for centralised metadata management and facilitates metadata sharing across multiple Databricks workspaces, improving data pipelines and governance.
- **Naming conventions and folder structure**: Improve code organisation and readability.
- **Implementing the "Is Active" Flag**:  The configuration file includes an "is active" flag to control which tables are processed by the pipeline. The pipeline checks this flag and only processes tables marked as active, providing more granular control over data ingestion.
- **Retry mechanisms**: Implement retries for failed tasks to ensure pipeline resilience.


This architecture offers a robust and scalable approach to building a data engineering pipeline for **Healthcare Revenue Cycle Management (RCM)** on **Azure**. By utilising **Azure Data Factory**, **Databricks**, and other Azure services, along with best practices in data engineering, the project enables healthcare organisations to gain valuable insights from their data and optimise their financial performance.

## Pipeline

### Pipeline Setup/Implementation or Storage Configuration

The data pipeline relies on mounting Azure Blob Storage containers to Databricks for various data stages (gold, silver, bronze, etc.). The following code mounts the required storage containers and ensures they are available for data processing: [Setup code](...)






---

# Conceptual Entity-Relationship Diagram (ERD) for Gold Layer Data Model

![image](https://github.com/user-attachments/assets/b4c5d251-ee9b-4767-9684-41bf6f0df806)


This section provides a conceptual **Entity-Relationship Diagram (ERD)** that represents the final data model in the **Gold layer** of the project. The ERD is designed to showcase the core entities and their relationships, which are crucial for generating reports and KPIs related to **patient encounters**, **transactions**, and **healthcare providers**.

### Key Entities and Relationships:

- **Patient**: Represents patients and their demographics, including details like insurance, medical history, and personal contact information.
- **Provider**: Represents healthcare providers, including doctors, specialists, and medical facilities, with relevant details such as specialties and affiliations.
- **Department**: Represents hospital departments or healthcare service units that provide various medical services.
- **Encounter**: Represents a patient visit to a provider, detailing the services rendered, diagnoses, procedures, and associated charges.
- **Transaction**: Represents financial transactions related to patient encounters, such as billings, payments (from both patients and insurance), and adjustments.
- **Claim**: Represents insurance claims associated with patient encounters, including claim status (approved, denied, or partially paid) and payment details.

### Relationships:

- **Patient to Encounter**: A one-to-many relationship, as a single patient can have multiple encounters with different providers or departments.
- **Provider to Encounter**: A one-to-many relationship, where a provider can have multiple encounters with different patients.
- **Encounter to Transaction**: A one-to-many relationship, as a single encounter can generate multiple financial transactions, including billing and payments.
- **Provider to Transaction**: A one-to-many relationship, as a provider can be associated with multiple transactions over time.
- **Claim to Encounter**: A one-to-one or one-to-many relationship, as an encounter may lead to a claim being filed with an insurance company.


 
### Use in Reporting:

This ERD serves as the foundational data model for generating insights into key performance indicators (KPIs) related to **Accounts Receivable (AR)**, **payment delays**, **uncollected payments**, and other financial metrics in healthcare revenue cycle management.

The final data model in the **Gold layer** aggregates and curates data from the **Bronze** and **Silver layers** to populate **fact** and **dimension** tables, which are optimised for business reporting and analytics. 


