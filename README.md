# End-to-End Data Engineering Pipeline for Healthcare Revenue Cycle Management (RCM) on Azure

![image](https://github.com/user-attachments/assets/a0db1b48-693e-4ce6-8f3d-c06d3c67d57b)

## Table of Contents

### 1. [Problem Statement](#1-problem-statement)
- [a. Overview of the RCM Process](#a-overview-of-the-rcm-process)
- [b. Key Challenges in Accounts Receivable (AR)](#b-key-challenges-in-accounts-receivable-ar)
- [c. Key Performance Indicators (KPIs) for AR](#c-key-performance-indicators-kpis-for-ar)
- [d. Data Engineering Objectives](#d-data-engineering-objectives)

### 2. [System Architecture](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/README.md#2-system-architecture-1)

### 3. [Conceptual Entity-Relationship Diagram (ERD)](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/README.md#3-conceptual-entity-relationship-diagram-erd-1)

### 4. [Medallion Architecture](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/README.md#medallion-architecture)

### 5. [Data Flow](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/README.md#data-flow)

### 6. [Best Practices] (https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/README.md#best-practices)

### 7. [Pipelines]


## 1. Problem Statement

The **Revenue Cycle Management (RCM)** process is vital for hospitals to manage the financial side of healthcare services, ensuring they can continue providing quality care while maintaining financial stability. RCM covers the entire process, starting from when a patient books an appointment through to when the provider receives payment. This project is focused on improving the **Accounts Receivable (AR)** aspect, which tracks the money owed to the hospital by patients and insurers.

### a. Overview of the RCM Process

1. **Patient Visit**: The RCM journey begins when a patient visits the hospital. During this visit, patient details, including **insurance information**, are collected. This helps the provider understand who is responsible for the bill – the **insurance company**, the **patient**, or both.

   **Example**:  
   - Total bill: **$20,000**  
   - Insurance pays: **$15,000**  
   - Patient owes: **$5,000**

2. **Services Provided**: After the patient visit, medical services are provided, and the hospital generates a bill.

3. **Billing and Claims Review**: The bill is sent to the insurance company for review. The insurance may approve the claim and pay in full, pay partially, or reject the claim entirely.

4. **Payments and Follow-ups**: If the insurance only covers part of the bill, the remaining balance is the patient's responsibility. The hospital will follow up with the patient to collect the outstanding amount.

5. **Tracking and Improvement**: The RCM process focuses on tracking unpaid AR and optimising the collection process, ensuring the hospital remains financially healthy.

### b. Key Challenges in Accounts Receivable (AR)

Managing **AR** is crucial for hospitals, but **patient payments** can pose significant risks. There are scenarios where the financial burden falls more heavily on patients, making it more difficult to collect outstanding payments. Some examples include:

- **Low Insurance Coverage**: When insurance providers place most of the financial responsibility on the patient, as is often the case with high-deductible policies or private clinics.
- **Dental and Private Treatments**: Non-covered services or treatments that require the patient to pay the full amount.
- **Deductibles**: When the patient is responsible for covering a portion of the medical costs before insurance kicks in.

The primary objectives of AR management are:

1. **Collect cash quickly** by securing payments as soon as possible.
2. **Minimise the collection period** – the longer the payment is overdue, the less likely the full amount will be collected (or, for example, a decrease in the overall value of the receivable due to the time value of money).

**Collection Probability** of the full amount decreases over time:
- **93%** of the owed amount is collected within **30 days**.
- **85%** is collected within **60 days**.
- **73%** is collected within **90 days**.

### c. Key Performance Indicators (KPIs) for AR

[Healthcare Accounts Receivable Management](https://mdmanagementgroup.com/healthcare-accounts-receivable-management/)

- **Risk Factors Analysis**: Analyse delayed payments and identify factors (e.g., insurance delays, patient financial responsibility) contributing to AR inefficiencies.
- **KPIs for AR**: Track critical KPIs such as outstanding payments, delayed claims, and payment status (e.g., paid, pending, denied).
To effectively manage AR, hospitals track KPIs such as:

1. **AR > 90 days**: This KPI measures how much of the total AR is overdue by more than 90 days.
   - **Example**:  
     - Total AR: **$1,000,000**  
     - AR > 90 days: **$200,000**  
     - Overdue Ratio: **200K / 1M = 20%**

2. **Days in AR**: This KPI tracks how long it takes to collect the AR.
   - **Example**:  
     - Total AR: **$1,000,000**  
     - AR collected in **100 days**:  
       - **$400,000 AR**  
       - **Collection rate**: **$10,000/day**  
       - **Target days in AR**: **45 days**

[Revenue Cycle KPIs, Definitions, and Benchmarks](https://gentem.com/blog/revenue-cycle-kpis-definitions-and-benchmarks/)

### d. Data Engineering Objectives

Given the complexity and variety of data sources involved in the RCM process (e.g., patient records, insurance claims, billing details, and payment transactions), there is a need to create a robust **end-to-end data pipeline**. This pipeline will collect, process, and structure data to enable accurate reporting on AR performance.

The primary goals of this project are to:

- **Ingest data** from various sources, such as patient records, insurance claims, billing information, and payment history.
- **Transform** raw data into structured formats to create **fact tables** and **dimension tables** that will support AR analysis.
- **Generate KPIs** like **AR > 90 days** and **Days in AR** to help the reporting team monitor and assess AR performance.
- Provide insights to help **reduce collection periods**, improve cash flow, and ultimately enhance the financial health of hospitals.

## 2. System Architecture

This project utilises a modern cloud-based data platform leveraging several Azure services:
- **Azure SQL Database** houses the source EMR data for the two hospitals.
- **Azure Data Lake Storage Gen2 (ADLS Gen2)** acts as the central data repository, storing data across the different layers of the Medallion architecture.
- **Azure Data Factory (ADF)** orchestrates the end-to-end data pipeline, including data ingestion, transformation, and loading.
- **Azure Databricks** provides a platform for data processing, cleansing, transformation, and creating fact and dimension tables, leveraging its integration with Delta Lake.
- **Azure Key Vault** securely stores sensitive information like access keys and connection strings, enhancing security and compliance.
- **Unity Catalog** serves as the centralised metadata repository, providing a unified view of data assets across the workspace.



## 3. Conceptual Entity-Relationship Diagram (ERD) 

![image](https://github.com/user-attachments/assets/eb71c620-4684-429c-8eae-d8f8b874cb74)


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
    - Development of **dimension tables**, like `dim_patient`, `dim_provider`, `dim_department`, `dim_encounter`, `dim_icd_codes`, `dim_claims`, `dim_cpt_codes`, and `dim_npi` to provide context and details for the facts.

- **Data Format**: Data is stored in **Delta tables**, optimised for reporting and analytical queries.

## Data Flow

1. **Landing Layer**: Claims and CPT data uploaded by insurance and third-party providers in CSV format.
2. **Bronze Layer**: Data from various sources (EMR databases, APIs) is ingested in **Parquet** format.
3. **Silver Layer**: Data is cleansed, standardised, and enriched using techniques like data quality checks, CDM implementation, and SCD Type 2. Data is stored in **Delta tables**.
4. **Gold Layer**: Data from the Silver layer is transformed into fact and dimension tables, also stored as **Delta tables** for optimised reporting.


## Best Practices

- **Metadata-driven pipeline design**: A configuration file defines data sources and pipeline parameters, enhancing reusability and maintainability.
- **Parallel pipeline execution**: The initial sequential data processing was slow due to the auto-incrementing key in the audit table. By removing the auto-incrementing key and enabling parallel processing in Azure Data Factory, the pipeline now runs much faster, significantly improving efficiency.
- **Implementing Azure Key Vault**:  Securely stores and accesses sensitive information such as passwords and access tokens and eliminates hardcoding of credentials in code, aligning with industry best practices.
- **Utilising Unity Catalog for Metadata Storage**: Replaces the local Hive metastore with Unity Catalog for centralised metadata management and facilitates metadata sharing across multiple Databricks workspaces, improving data pipelines and governance.
- **Naming conventions and folder structure**: Improve code organisation and readability.
- **Implementing the "Is Active" Flag**:  The configuration file includes an "is active" flag to control which tables are processed by the pipeline. The pipeline checks this flag and only processes tables marked as active, providing more granular control over data ingestion.
- **Retry mechanisms**: Implement retries for failed tasks to ensure pipeline resilience.


## Pipeline

### Pipeline Setup/Implementation or Storage Configuration

The data pipeline relies on mounting Azure Blob Storage containers to Databricks for various data stages (gold, silver, audit, etc.). The following code mounts the required storage containers and ensures they are available for data processing: [See Setup code](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/Notebooks/1-%20Setup/2.%20adls_mount.py)


