# Generic Datasets in Metadata-Driven Pipeline

Our pipeline leverages **generic, reusable datasets** that adapt to different sources through parameterization. This approach allows for a flexible, scalable, and maintainable pipeline design. Below is an overview of how each dataset is structured and used in the pipeline:

## **1. Generic SQL Dataset**

- **Purpose:**  
  Handles any table from **Azure SQL DB** by parameterising the database, schema, and table names.

- **Key Parameters:**
  - **Database name**
  - **Schema name**
  - **Table name**

- **Reusability:**  
  A single dataset definition can serve multiple tables by passing the appropriate parameters at runtime.
<img width="710" alt="image" src="https://github.com/user-attachments/assets/60a07c6f-d267-4097-9945-bc735cac1942" />

---

## **2. Generic ADLS Parquet Dataset**

- **Purpose:**  
  Manages **Parquet files** in **Azure Data Lake Storage (ADLS)** for efficient storage and retrieval.

- **Key Parameters:**
  - **Container name**
  - **File path**
  - **File name**

- **Format:**  
  Optimised for **Parquet format**, which is both storage-efficient and fast for querying.

---

## **3. Generic Flat File Dataset** 

- **Purpose:**  
  Processes **delimited text files** (Config file) stored in ADLS or other cloud storage locations.

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
