## **Linked Services**

As part of the **metadata-driven architecture** and to enhance the flexibility and maintainability of ADF pipelines, the **linked services** are designed to work within this framework. This approach ensures that configuration information is stored externally and referenced within the pipelines, avoiding the need to hardcode values.

### How the Metadata-Driven Architecture Applies to Linked Services

- **Parameterisation**: Instead of hardcoding values such as database names, file paths, or storage keys directly into the linked services, these values are passed as parameters. This allows a single linked service to connect to multiple sources or targets by dynamically changing the parameters at runtime.
- **Configuration Files**: Specific parameter values are stored in external configuration files as delimited text (CSV) files. These are stored in Azure Data Lake Storage Gen2 (ADLS Gen2). The ADF pipeline uses a lookup activity to read these configuration files and retrieve the necessary values to populate the linked service parameters.

### Key Linked Services

This project uses **four primary linked services**:

1. **Azure SQL DB Linked Service (Source)**:  
   Connects to the Azure SQL databases that hold the EMR data. The database name is passed as a parameter, enabling the linked service to connect to both Hospital A and Hospital B databases. 
![image](https://github.com/user-attachments/assets/2aa5df3b-47af-47a5-bf52-d60dd8c6441f)

2. **ADLS Gen2 Linked Service (Target)**:  
   Connects to the Azure storage account where raw data, Parquet files, and configuration files are stored. 

3. **Azure Databricks Delta Lake Linked Service**:  
   Interacts with Delta tables in Azure Databricks, especially for managing the audit table that tracks pipeline runs.

4. **Linked Service for Key Vault**:
  A linked service for Key Vault in ADF grants the Data Factory service principal the necessary access permissions under the Key Vault's Access Policies to enable the Data Factory to access secrets during the linked service creation process.

5. **Linked Service for Databricks (to Execute Notebooks)**:
   This linked service is for running data transformations and processing logic defined in Databricks notebooks as part of a larger data pipeline orchestrated by Data Factory. The Databricks linked service is updated to use the secret scope for credentials instead of directly providing the storage account ke
