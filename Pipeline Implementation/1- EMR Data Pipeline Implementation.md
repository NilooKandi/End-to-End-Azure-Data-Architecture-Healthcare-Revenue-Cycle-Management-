## Overview

A metadata-driven pipeline built using **ADF** that processes EMR data from **Azure SQL DB**, storing it in **Parquet format** in the **bronze layer** in **ADLS Gen2**.

## **Pipeline Components**
The audit table (Delta Table) was implemented as a core component to enable intelligent data processing decisions and maintain data governance through tracking load history, making the pipeline truly metadata-driven. It serves as a critical monitoring system - tracking pipeline success, enabling performance monitoring, supporting troubleshooting, and verifying data completeness, thereby ensuring reliable and traceable data processing throughout the pipeline.

### 1. Linked Services

- **Azure SQL DB** (Source)
- **ADLS Gen2** (Target)
- **Delta Lake** (Delta Lake where the audit table is stored) [See Audit Table Code here](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/audit_table_ddl.txt)
- **Key Vault** (Secure Credential Management)
  
### 2. **Datasets**

Our pipeline leverages **generic, reusable datasets** that adapt to different sources through parameterisation. This approach allows for a flexible, scalable, and maintainable pipeline design. Below is an overview of how each dataset is structured and used in the pipeline:

## **1. Generic SQL Dataset**

- **Purpose:**  
  Handles any table from **Azure SQL Database** by parameterizing the database, schema, and table names.

- **Key Parameters:**
  - **Database name**
  - **Schema name**
  - **Table name**

- **Reusability:**  
  A single dataset definition can serve multiple tables by passing the appropriate parameters at runtime.
![image](https://github.com/user-attachments/assets/9cdd8cb1-7390-4a64-b3b8-18ca593d8986)

---

## **2. Generic ADLS Parquet Dataset**

- **Purpose:**  
  Manages **Parquet files** in **Azure Data Lake Storage (ADLS)** for efficient storage and retrieval.

- **Key Parameters:**
  - **Container name**
  - **File path**
  - **File name**

- **Format:**  
  Optimized for **Parquet format**, which is both storage-efficient and fast for querying.

---

## **3. Generic Flat File Dataset**

- **Purpose:**  
  Processes **delimited text files** (e.g., CSV) stored in ADLS or other cloud storage locations.

- **Key Parameters:**
  - **Container name**
  - **File path**
  - **File name**
  - **Delimiter**

- **Flexibility:**  
  Adapts to various text file formats by allowing different delimiters (e.g., comma, tab) and file locations.

---

## **Benefits**

- **Reduces Code Redundancy:**  
  By using parameterized datasets, we avoid creating separate dataset definitions for each source.

- **Simplifies Maintenance:**  
  Changes to the dataset structure are handled in one place, reducing maintenance overhead.

- **Enables Easy Scaling:**  
  New sources can be added by simply passing different parameters, making the pipeline easily extensible.

- **Streamlines Pipeline Configuration:**  
  The use of generic datasets simplifies the overall configuration, as you don't need to define separate datasets for each data source.

- **Promotes Consistent Data Handling:**  
  Consistent dataset management ensures that all data is processed using the same structure, improving reliability and reducing errors.

---

This **generic dataset approach** empowers the pipeline to process multiple data sources efficiently without the need to create separate dataset definitions for each one, thus promoting scalability and maintainability.


# Dataset Types Overview

This project utilises the following dataset types:

- **Azure SQL DB:** (Source database tables, including database name, schema, and table name).
- **Delimited Text Dataset:** (Configuration files stored in **ADLS Gen 2** in CSV format). 
- **Parquet Dataset:** (Transformed data stored in **ADLS Gen 2** in **Parquet format**).
- **Delta Lake Dataset:** (Used for **audit logging and tracking** in **Delta Lake** on **ADLS Gen 2**). 


### **1. Configuration**
- A **configuration file** is stored as a **CSV** in the `configs/EMR` folder within the ADLS Gen 2 storage account.
- The configuration file contains metadata information such as:
  - **Source database**, **Schema**, and **tables**
  - **Load type** (either **incremental** or **full**)
  - **Watermark columns** for incremental loads
  - **Target path** in the **bronze layer**

### **2. Data Factory Pipeline**
- The **Azure Data Factory (ADF)** pipeline orchestrates the data flow:
  1. A **Lookup activity** is used to read the configuration file.
  2. A **ForEach activity** iterates over each entry in the configuration file, where each entry corresponds to a table to be ingested.

### **3. File Check and Archiving**
- For each table, the pipeline performs a **Get Metadata activity** to check whether a **Parquet file** already exists in the bronze layer.
- If a file exists, it is moved to an **archive folder** before proceeding with the next steps.

### **4. Load Type Determination**
- The pipeline uses an **If Condition activity** to determine whether to perform a **full load** or **incremental load**, based on the `load type` in the configuration file.

  - **Full Load:**
    - A **Copy Data activity** extracts all records from the source table in **Azure SQL DB** using a `SELECT *` query.
    - A new column, `data source`, is added to track the hospital source (e.g., Hospital A or Hospital B).
    - Data is written as a **Parquet file** to the specified target path in the **bronze layer**.
    - After the data is copied, the pipeline logs the load information to an **audit table** in **Delta Lake**.

  - **Incremental Load:**
    - The pipeline queries an **audit table** in **Delta Lake** to retrieve the **last load time**.
    - This timestamp serves as a **watermark** to select only new or updated records from the source Azure SQL DB.
    - A **Copy Data activity** processes the incremental records and writes them to the bronze layer in **Parquet format**.
    - The audit table is updated with the **current timestamp**, reflecting the time of the latest load.

### **5. Parallel Execution and Future Improvements**
- Initially, the pipeline was designed to run **sequentially**, as the **auto-increment ID column** in the audit table limited parallel updates.
- In the second part of the tutorial, the speaker outlines plans to **remove the auto-increment column** to allow for **parallel execution** of the pipeline, improving performance and scalability.
- The pipeline is designed to be **generic** and **metadata-driven**, making it adaptable to various tables and load types as defined in the configuration file.

### **Key Features**
- **Metadata-Driven:** The pipeline adapts to different tables and load types by reading from the configuration file.
- **Scalability:** The pipeline is designed to scale by processing large healthcare datasets and supporting parallel execution in future iterations.
- **Audit Logging:** Delta Lake is used to log load details, ensuring traceability and tracking for each data ingestion process.

