## **Part 2: End-to-End Pipeline (pl_end_to_end_healthcare pipeline)**

The **pl_end_to_end_healthcare pipeline** orchestrates the entire data flow, including the loading of EMR data to the bronze layer, processing of claims data, loading of code data, and transforming data to the silver and gold layers.

### Pipeline Breakdown

- **Execution of source_to_bronze pipeline**:  
  The pipeline begins by executing the previously described `source_to_landing` (renamed to `source_to_bronze`) pipeline to bring the EMR data into the bronze layer.

- **Databricks Notebooks Execution**:  
  It then executes various Databricks notebooks to handle different data processing tasks:

  - **Claims Data**: Load claims data from the landing zone (CSV) into the bronze layer (Parquet).
  - **Code Data**: Load CPT codes from the landing zone into the bronze layer and call APIs to load NPI and ICD codes directly into the bronze layer.
  - **Silver Layer Processing**: Execute notebooks to clean, transform, and enrich data from the bronze layer, storing it as Delta tables in the silver layer. This includes implementing a common data model and applying **Slowly Changing Dimension Type 2 (SCD Type 2)** to handle changes in dimension tables.
  - **Gold Layer Processing**: Execute notebooks to create fact and dimension tables from the silver layer data and store them as Delta tables in the gold layer. These tables are optimised for reporting and business intelligence.

### Enhancements Made in Part 2

- **Azure Key Vault Integration**:  
  Passwords and access tokens are stored securely in **Azure Key Vault** and referenced dynamically within the linked services, ensuring security and ease of management.

- **Parallel Execution**:  
  The `source_to_bronze` pipeline has been modified to execute the **ForEach** activity in parallel, significantly reducing processing time. This improvement was made possible by removing the auto-incrementing ID column in the audit table.

- **Active Flag Implementation**:  
  The pipeline logic now checks the `is_active` flag in the configuration file to determine whether a data source should be processed. This provides more control over which data sources are included in the pipeline.


- **Unity Catalog Integration**:  
  The **Hive metastore** has been replaced with **Unity Catalog**, which provides a centralised metadata repository that can be accessed by multiple Databricks workspaces, improving metadata management and collaboration.

---

This pipeline provides a comprehensive, end-to-end solution for processing and transforming healthcare data, with optimised performance and secure management of sensitive information.
