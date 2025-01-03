## Project Datasets Overview

This project utilises diverse datasets from multiple sources to build a comprehensive data pipeline focused on **Healthcare Revenue Cycle Management (RCM)**. The datasets are processed and transformed through the **Landing**, **Bronze**, **Silver**, and **Gold** layers of the **medallion architecture** to generate actionable insights and KPIs. Below is an overview of the key datasets involved in this project:

## **1. Electronic Medical Records (EMR) Data**
- **Source:** Azure SQL DB (Two separate databases representing two hospitals: Hospital A and Hospital B)
- **Format:** Initially stored as tables in Azure SQL DB, ingested into the **Bronze layer** in **Parquet format**.
- **Content:**
  - **Patients:** Patient demographics, insurance details, and contact information.
  - **Providers:** Doctor information, specializations, and affiliations.
  - **Departments:** Hospital department details.
  - **Transactions:** Financial records of patient billing, payments, and adjustments.
  - **Encounters:** Details of each patient visit, including diagnoses, procedures, and associated charges.

## **2. Claims Data**
- **Source:** CSV flat files uploaded by insurance companies (payers) to the **Landing folder** in **Azure Data Lake Storage (ADLS) Gen 2**.
- **Format:** Initially in **CSV format**, then converted to **Parquet format** in the **Bronze layer**.
- **Content:** 
  - Details of insurance claims filed for patient encounters.
  - Contains claim IDs, patient and provider information, claim status, and payment details.

## **3. NPI Data (National Provider Identifier)**
- **Source:** Public API for the National Provider Identifier (NPI).
- **Format:** Directly ingested into the **Bronze layer** in **Parquet format**.
- **Content:** 
  - Provides detailed information about healthcare providers, including their NPI number, name, position, organisation affiliation, and status updates.

## **4. ICD Data (International Classification of Diseases)**
- **Source:** Public API for **ICD codes**.
- **Format:** Directly ingested into the **Bronze layer** in **Parquet format**.
- **Content:** 
  - Maps **ICD codes** to their corresponding descriptions, enabling the identification of specific diagnoses related to patient encounters.

## **5. CPT Code Data (Current Procedural Terminology)**
- **Source:** Public API for **CPT codes**.
- **Format:** Directly ingested into the **Bronze layer** in **Parquet format**.
- **Content:**
  - Stores **CPT codes** used to represent medical procedures and services performed during patient encounters.

  [Difference between ICD codes CPT codes](https://www.simplepractice.com/blog/icd-codes-and-cpt-codes/)
---

### **Data Pipeline Architecture**
These datasets are ingested and transformed through the following layers of the **medallion architecture**:

1. **Landing Layer:** Raw data is ingested in its original format (CSV, JSON, etc.) before any transformation or cleaning.
2. **Bronze Layer:** Data is cleaned and converted into **Parquet format**, making it more efficient for analytics.
3. **Silver Layer:** Data is enriched and joined with other datasets to create more refined, intermediate datasets.
4. **Gold Layer:** The final output consists of **fact and dimension tables** that can be used to generate **valuable insights** and **KPIs** for optimszing the **Revenue Cycle Management (RCM)** process.

These transformed datasets are used for reporting, analysis, and machine learning, providing insights into financial, clinical, and operational performance across the healthcare system.
