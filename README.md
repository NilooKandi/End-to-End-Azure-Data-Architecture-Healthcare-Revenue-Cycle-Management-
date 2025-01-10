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





