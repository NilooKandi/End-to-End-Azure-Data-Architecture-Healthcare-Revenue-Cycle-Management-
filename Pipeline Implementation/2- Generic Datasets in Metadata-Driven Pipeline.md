# Generic Datasets in Metadata-Driven Pipeline

Our pipeline leverages **generic, reusable datasets** that adapt to different sources through parameterization. This approach allows for a flexible, scalable, and maintainable pipeline design. Below is an overview of how each dataset is structured and used in the pipeline:

## **1. Generic SQL Dataset**

- **Purpose:**  
  Handles any table from **Azure SQL DB** by parameterising the database, schema, and table names.

- **Key Parameters:**
  - **Database name**
  - **Schema name**
  - **Table name**

<img width="710" alt="image" src="https://github.com/user-attachments/assets/60a07c6f-d267-4097-9945-bc735cac1942" />

---

## **2. Generic ADLS Parquet Dataset**

- **Purpose:**  
  Manages **Parquet files** in **Azure Data Lake Storage (ADLS)** for efficient storage and retrieval.

- **Key Parameters:**
  - **Container name**
  - **File path**
  - **File name**


---

## **3. Generic Flat File Dataset** 

- **Purpose:**  
  Processes **delimited text files** stored in ADLS or other cloud storage locations. [Config file](https://github.com/NilooKandi/End-to-End-Azure-Data-Architecture-Healthcare-Revenue-Cycle-Management-/blob/main/Datasets/load_config.csv)

- **Key Parameters:**
  - **Container name** 'configs'
  - **File path**
  - **File name**

![image](https://github.com/user-attachments/assets/0794c8ed-1d4b-4484-b63f-64adf6d59746)

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
