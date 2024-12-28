# Solution Architecture for Healthcare Revenue Cycle Management (RCM)

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
    - **Common Data Model (CDM)**: Standardizes data from different sources to resolve schema discrepancies and potential ID clashes (e.g., patient tables from different hospitals).
    - **Slowly Changing Dimension (SCD) Type 2**: Maintains historical records, ensuring that changes in patient demographics or other data are properly tracked.

- **Data Format**: Data is stored in **Delta tables** in the Silver layer, which provide ACID transactions, enabling updates and improved data quality management.

### 4. Gold Layer (Facts and Dimensions)

- **Purpose**: The Gold layer contains curated, aggregated data modeled into fact and dimension tables for business reporting and KPI generation.

- **Key Processes**:
    - Creation of **fact tables**, such as `fact_transaction`, to capture key business events.
    - Development of **dimension tables**, like `dim_patient`, `dim_provider`, and `dim_department`, to provide context and details for the facts.

- **Data Format**: Data is stored in **Delta tables**, optimized for reporting and analytical queries.

## Data Flow

1. **Bronze Layer**: Data from various sources (EMR databases, claims files, APIs) is ingested in **Parquet** format.
2. **Silver Layer**: Data is cleansed, standardized, and enriched using techniques like data quality checks, CDM implementation, and SCD Type 2. Data is stored in **Delta tables**.
3. **Gold Layer**: Data from the Silver layer is transformed into fact and dimension tables, also stored as **Delta tables** for optimized reporting.

## Key Technologies

- **Azure Data Factory**: Orchestrates data movement and pipeline execution, including invoking Databricks notebooks for data transformations.
- **Azure Databricks**: Provides a platform for data processing and transformation using **Python** and **Apache Spark**. Databricks notebooks are used to implement data cleaning, CDM, SCD Type 2, and the creation of fact and dimension tables.
- **Azure SQL DB**: Stores the initial **EMR data** for the two hospitals.
- **Azure Data Lake Storage (ADLS) Gen 2**: Serves as the primary storage for raw and processed data across all layers (Landing, Bronze, Silver, and Gold).
- **Azure Key Vault**: Securely stores sensitive credentials, like access keys and API tokens, to enhance security and prevent hardcoding.
- **Unity Catalog**: Enables centralized metadata management for the data lake, improving discoverability, data lineage, and collaboration.

## Best Practices

- **Metadata-driven pipeline design**: A configuration file defines data sources and pipeline parameters, enhancing reusability and maintainability.
- **Parallel pipeline execution**: Tasks are executed concurrently where possible, improving performance.
- **Retry mechanisms**: Implement retries for failed tasks to ensure pipeline resilience.
- **Naming conventions and folder structure**: Improve code organisation and readability.

---

This architecture offers a robust and scalable approach to building a data engineering pipeline for **Healthcare Revenue Cycle Management (RCM)** on **Azure**. By utilizing **Azure Data Factory**, **Databricks**, and other Azure services, along with best practices in data engineering, the project enables healthcare organisations to gain valuable insights from their data and optimise their financial performance.

