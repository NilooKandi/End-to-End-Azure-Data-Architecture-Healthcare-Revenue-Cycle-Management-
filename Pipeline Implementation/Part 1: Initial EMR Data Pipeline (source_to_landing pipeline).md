## **Part 1: Initial EMR Data Pipeline (source_to_landing pipeline)**

The **source_to_landing pipeline** in Azure Data Factory (ADF) orchestrates the movement and transformation of data. This pipeline is designed with a **metadata-driven architecture**, which enhances flexibility and maintainability by dynamically adapting to different data sources and targets based on external configuration files. Below is a breakdown of the pipeline structure based on the source:

### Pipeline Breakdown

1. **Read Configuration File (Lookup Activity)**:  
   The pipeline begins by reading the configuration file (`configs/emr/load_config.csv`) stored in **ADLS Gen2**. This file contains metadata about the data sources and targets, such as database names, table names, load types (full or incremental), and target paths in ADLS Gen2.
   
![image](https://github.com/user-attachments/assets/6be77b6e-5e4f-4ad5-9ebf-0f1ea413f5df)

![image](https://github.com/user-attachments/assets/c6a2c016-880c-4375-bbac-c09cad32ee60)


2. **Iterate through Each Data Source (ForEach Activity)**:  
   The pipeline then iterates through each row in the configuration file, processing one data source (table) at a time.

3. **Check for Existing Data (Get Metadata Activity)**:  
   For each table, the pipeline checks if a Parquet file already exists in the **bronze layer** of ADLS Gen2. If the file exists, it is moved to an archive folder to avoid overwriting existing data.

4. **Check Active Flag (If Condition Activity)**:  
   The pipeline checks if the `is_active` flag for the data source in the configuration file is set to `1`. If it is not set, the data source is skipped, allowing for selective processing of tables based on the configuration.

5. **Determine Load Type (If Condition Activity)**:  
   The pipeline checks the `load_type` field from the configuration file to determine if a full load or an incremental load is required.

   - **Full Load**: If `load_type` is `full`, a copy activity reads the entire table from the Azure SQL database and writes it as a Parquet file to the **bronze layer** in ADLS Gen2.
   - **Incremental Load**: If `load_type` is `incremental`, the pipeline first retrieves the latest load timestamp from the audit table (stored as a Delta table in Databricks). A copy activity then reads only the new or updated records from the Azure SQL Database based on a watermark column (typically a last modified date) and appends them to the existing Parquet file in the bronze layer.

6. **Update Audit Table**:  
   After each data load (whether full or incremental), the audit table in **Databricks** is updated with details of the load, including the timestamp, the number of rows copied, and the watermark column used.

---

This pipeline structure follows a clear, modular flow that leverages **metadata-driven architecture** to ensure scalability and easy maintenance, enabling flexible data processing without hardcoded values.
