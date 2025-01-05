# Azure Data Engineering Project - Healthcare Domain

This repository contains a comprehensive Azure Data Engineering solution for the healthcare domain. The project leverages Azure services such as Azure Data Factory, Azure Databricks, Azure SQL Database, and Azure Data Lake Storage, with a focus on Accounts Receivable (AR) management and Key Performance Indicators (KPIs) generation. Below is the structure of the project, its architecture, and step-by-step implementation.

## Table of Contents

### **1. Project Overview**

#### Healthcare Domain Introduction
- Sets the stage by explaining the relevance of **Accounts Receivable (AR) management** and **KPIs** in the healthcare industry.
  
#### Project Objectives
- Defines the clear objectives of the project, clarifying the purpose and desired outcomes of this Azure Data Engineering implementation.
  
#### Architecture Overview
- A high-level overview of the **Medallion Architecture** and its different layers:
  - **Landing Zone**: Raw data ingestion
  - **Bronze Layer**: Storing data in Parquet format
  - **Silver Layer**: Data cleaning and transformation in Delta format
  - **Gold Layer**: Fact and dimension tables for reporting and analysis
  
#### ER Diagram
- A visual representation of the target data model, including **fact** and **dimension tables**, for stakeholders to understand the relationships between the data.

---

### **2. Infrastructure Setup**

#### Azure Resources Setup
- **Azure Data Factory (ADF)**: Orchestration tool for managing data pipelines.
- **Azure Databricks**: Platform for data processing and transformation.
- **Azure Data Lake Storage Gen2 (ADLS Gen2)**: Storage for raw, intermediate, and processed data.
- **Azure SQL Database**: The source database for EMR data.
- **Azure Key Vault**: Secure storage for credentials and connection strings.

#### Security Implementation
- **Key Vault Configuration**: Setting up Azure Key Vault for storing sensitive information like database credentials.
- **App Registrations (ADF and Databricks)**: Registering Azure Data Factory and Databricks applications to access Key Vault secrets.
- **Access Policies**: Defining granular access policies for controlling permissions in Azure Key Vault.
- **Managing Secrets**: Demonstrating how to securely retrieve and use secrets from Key Vault in ADF and Databricks.

---

### **3. Data Sources**

#### EMR Data (Azure SQL DB)
- Describes the structure and location of **EMR data** in Azure SQL DB, covering relevant tables such as **Patients**, **Providers (Doctors)**, **Department**, **Transactions**, and **Encounters**.

#### Claims Data (CSV Files)
- Explains the format and source of **Claims Data** (CSV files) stored in Azure Data Lake Storage (ADLS Gen2).

#### NPI Data (API)
- Describes the **National Provider Identifier (NPI)** data and its API source.

#### ICD Data (API)
- Explains the **International Classification of Diseases (ICD)** codes and descriptions, and their API source.

#### Data Schema Details
- Provides detailed information about the schema of each data source, ensuring understanding of the data structure across teams.

---

### **4. Data Pipeline Architecture**

#### Medallion Architecture Implementation
- Detailed explanation of the **Medallion Architecture** for managing data through three key layers:
  - **Landing Zone**: Raw data ingestion.
  - **Bronze Layer**: Raw data in Parquet format, serving as the "source of truth."
  - **Silver Layer**: Cleaned, transformed, and enriched data stored in Delta tables.
  - **Gold Layer**: Fact and dimension tables, used for reporting and KPI generation.

#### Unity Catalog Implementation
- **Centralized Metadata Management**: Utilizing **Unity Catalog** for managing metadata across different workspaces.
- **Cross-workspace Access**: Enabling metadata access from multiple Databricks workspaces.
- **Data Governance**: Strengthening data governance by maintaining a single source of truth for metadata.

---

### **5. Pipeline Implementation**

#### Configuration Management
- **Configuration File Structure**: Description of the configuration file (`load_config.csv`) that manages pipeline parameters such as **load type**, **watermark column**, and **target path**.
- **Active/Inactive Flag Logic**: Explanation of how the **Active/Inactive flag** determines which tables are processed in the pipeline.

#### Source to Landing Zone
- Describes how data is ingested into the **Landing Zone** from various sources (e.g., API, SQL DB, CSV files).

#### Landing to Bronze Layer
- **Data Ingestion Patterns**: Discussing batch and streaming data ingestion patterns.
- **File Format Conversions**: Converting CSV files to **Parquet** for optimized storage and query performance.

#### Bronze to Silver Layer
- **Data Quality Checks**: Implementing checks to ensure data accuracy and consistency.
- **SCD Type 2 Implementation**: Using **Slowly Changing Dimension Type 2** for maintaining historical data.
- **Data Quarantine Process**: Explaining the process of quarantining bad data for further analysis.

#### Silver to Gold Layer
- **Fact and Dimension Tables**: Creation of **fact** and **dimension tables** based on the defined data model.
- **Data Transformations**: Performing necessary transformations and aggregations for reporting.

---

### **6. Monitoring and Logging**

#### Audit Table Implementation
- Implementing an **audit table** to track pipeline runs and monitor data lineage.

#### Pipeline Monitoring
- Describes how to monitor **pipeline execution** and identify any issues or failures.

#### Error Handling
- Implementing error handling mechanisms to ensure data integrity and graceful recovery from failures.

---

### **7. Query Examples**

#### KPI Calculations
- Demonstrating how to calculate KPIs using data from the **Gold Layer**.

#### Sample Reports
- Showcasing sample reports generated using the Gold Layer data, covering key healthcare metrics.

#### Performance Optimization
- Techniques for **optimizing queries** to improve performance for faster reporting and analysis.

---

### **8. Source Control and DevOps**

#### GitHub Integration
- **Version Control**: Integrating the project code with **GitHub** for version control and collaboration.
  
#### Deployment Process
- Describes the process of deploying the pipeline and related code into different environments.

#### Version Control
- Emphasizes the importance of version control for **tracking changes** and managing code rollbacks.

---

### **9. Limitations and Future Enhancements**

#### Data Discrepancies
- Acknowledging potential **data discrepancies** due to the use of synthetic data or the Faker module for generating test data.

#### Additional KPIs and Data Models
- Identifying opportunities for incorporating **additional KPIs** and expanding the data model to handle more complex use cases.

---

