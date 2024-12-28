## Project Datasets Overview

The project uses diverse datasets from various sources to build a comprehensive data pipeline for **Healthcare Revenue Cycle Management (RCM)**. Here's an overview of these datasets:

### 1. Electronic Medical Records (EMR) Data

* **Source:** Azure SQL DB (Two separate databases representing two hospitals: Hospital A and Hospital B)  
* **Format:** Initially stored as tables in Azure SQL DB, then ingested into the Bronze layer in Parquet format.  
* **Content:** Includes crucial information about patients, providers, departments, transactions, and encounters:  
  - **Patients:** Patient demographics, insurance details, and contact information.  
  - **Providers:** Doctor information, specialisations, and affiliations.  
  - **Departments:** Hospital department details.  
  - **Transactions:** Financial records of patient billing, payments, and adjustments.  
  - **Encounters:** Details of each patient visit, including diagnoses, procedures, and associated charges.

### 2. Claims Data

* **Source:** CSV flat files uploaded by insurance companies (payers) to the Landing folder in the Azure Data Lake Storage (ADLS) Gen 2.  
* **Format:** Initially as CSV files, then converted to Parquet format in the Bronze layer.  
* **Content:** Contains details about insurance claims filed for patient encounters, including claim IDs, patient and provider information, claim status, and payment details.

### 3. NPI Data

* **Source:** Public API for the National Provider Identifier (NPI).  
* **Format:** Directly ingested into the Bronze layer in Parquet format.  
* **Content:** Provides comprehensive information about healthcare providers, including their NPI number, name, position, organisation affiliation, and updated status.

### 4. ICD Data

* **Source:** Public API for the International Classification of Diseases (ICD) codes.  
* **Format:** Directly ingested into the Bronze layer in Parquet format.  
* **Content:** Maps ICD codes to their corresponding descriptions, enabling the identification of specific diagnoses associated with patient encounters.

### 5. CPT Code Data

* **Source:** Public API.  
* **Format:** Directly ingested into the Bronze layer in Parquet format.  
* **Content:** Stores Current Procedural Terminology (CPT) codes, which are used to represent medical procedures and services performed during patient encounters.

---

These datasets are ingested and transformed through the **Bronze**, **Silver**, and **Gold** layers of the medallion architecture. The final output in the **Gold** layer consists of **fact** and **dimension tables**, which can be used to generate valuable insights and KPIs for optimizing the RCM process.
