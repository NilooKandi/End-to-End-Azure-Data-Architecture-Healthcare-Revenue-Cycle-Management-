# End-to-End Data Engineering Pipeline for Healthcare Revenue Cycle Management (RCM) on Azure

![image](https://github.com/user-attachments/assets/a0db1b48-693e-4ce6-8f3d-c06d3c67d57b)


## Table of Contents

### 1. [Project Overview](#Project Overview)
- [Healthcare Domain Introduction](#healthcare-domain-introduction)
  - [AR Management Overview](#ar-management-overview)
  - [KPI Requirements](#kpi-requirements)
- [Project Objectives & Scope](#project-objectives--scope)
- [System Architecture](#system-architecture)
  - [Medallion Architecture Overview](#medallion-architecture-overview)
  - [Component Interaction](#component-interaction)
- [Data Model](#data-model)
  - [ER Diagram](#er-diagram)
  - [Star Schema Design](#star-schema-design)

### 2. [Infrastructure Setup](#2-infrastructure-setup)
#### A. [Azure Resources Creation](#a-azure-resources-creation)
- [Resource Group Setup](#resource-group-setup)
- [Storage Account (ADLS Gen2)](#storage-account-adls-gen2)
  - [Container Structure](#container-structure)
  - [Access Configuration](#access-configuration)
- [Azure SQL Database](#azure-sql-database)
- [Azure Data Factory](#azure-data-factory)
- [Azure Key Vault](#azure-key-vault)
- [Azure Databricks Workspace](#azure-databricks-workspace)

#### B. [Security Implementation](#b-security-implementation)
- [Key Vault Configuration](#key-vault-configuration)
- [App Registrations](#app-registrations)
  - [ADF Application](#adf-application)
  - [Databricks Application](#databricks-application)
- [Access Policies Setup](#access-policies-setup)
- [Secret Management](#secret-management)

#### C. [Unity Catalog Setup](#c-unity-catalog-setup)
- [Metastore Creation](#metastore-creation)
  - [Storage Configuration](#storage-configuration)
  - [Access Connector Setup](#access-connector-setup)
- [Catalog Implementation (tt_hc_adb_ws)](#catalog-implementation-tt_hc_adb_ws)
- [Schema Organization](#schema-organization)
  - [Audit Schema](#audit-schema)
  - [Bronze Schema](#bronze-schema)
  - [Silver Schema](#silver-schema)
  - [Gold Schema](#gold-schema)

#### D. [Databricks Environment Setup](#d-databricks-environment-setup)
- [Mounting Configuration](#mounting-configuration)
  - [Storage Connection](#storage-connection)
  - [Container Access](#container-access)
- [Notebook Structure](#notebook-structure)
  - [Setup Scripts](#setup-scripts)
  - [API Extract Scripts](#api-extract-scripts)
  - [Transformation Scripts](#transformation-scripts)
- [Access Token Generation](#access-token-generation)

### 3. [Data Sources](#3-data-sources)
- [EMR Data (SQL DB)](#emr-data-sql-db)
- [Claims Data (CSV)](#claims-data-csv)
- [NPI Data (API)](#npi-data-api)
- [ICD Data (API)](#icd-data-api)
- [Schema Details & Mapping](#schema-details--mapping)

### 4. [Pipeline Architecture](#4-pipeline-architecture)
#### A. [Metadata-Driven Framework](#a-metadata-driven-framework)
- [Configuration Management](#configuration-management)
- [Parameter Design](#parameter-design)
- [Active/Inactive Flag System](#activeinactive-flag-system)

#### B. [Layer Implementation](#b-layer-implementation)
- [Landing Zone](#landing-zone)
- [Bronze Layer](#bronze-layer)
- [Silver Layer](#silver-layer)
- [Gold Layer](#gold-layer)

### 5. [Pipeline Implementation](#5-pipeline-implementation)
#### A. [ADF Components](#a-adf-components)
- [Linked Services](#linked-services)
- [Datasets](#datasets)
- [Dynamic Parameters](#dynamic-parameters)

#### B. [Data Movement](#b-data-movement)
- [Source to Landing](#source-to-landing)
- [Landing to Bronze](#landing-to-bronze)
- [Bronze to Silver](#bronze-to-silver)
- [Silver to Gold](#silver-to-gold)

#### C. [Transformation Logic](#c-transformation-logic)
- [Data Quality Rules](#data-quality-rules)
- [SCD Type 2 Implementation](#scd-type-2-implementation)
- [Delta Lake Integration](#delta-lake-integration)

### 6. [Monitoring & Logging](#6-monitoring--logging)
- [Audit Framework](#audit-framework)
- [Pipeline Monitoring](#pipeline-monitoring)
- [Error Handling](#error-handling)
- [Performance Tracking](#performance-tracking)

### 7. [Data Analysis](#7-data-analysis)
- [KPI Implementation](#kpi-implementation)
- [Report Generation](#report-generation)
- [Query Optimization](#query-optimization)

### 8. [DevOps Integration](#8-devops-integration)
- [GitHub Setup](#github-setup)
- [Deployment Strategy](#deployment-strategy)
- [Version Control](#version-control)
- [CI/CD Pipeline](#cicd-pipeline)

### 9. [Future Scope](#9-future-scope)
- [Known Limitations](#known-limitations)
- [Enhancement Plans](#enhancement-plans)
- [Scaling Considerations](#scaling-considerations)





## 1- Project Overview

This project implements an end-to-end Azure data engineering pipeline to optimise the Healthcare Revenue Cycle Management (RCM) process. The pipeline ingests, processes, and transforms data from various sources, including electronic medical records (EMR), claims data, and medical code APIs, to generate insightful reports and KPIs for improved financial performance.


# Conceptual Entity-Relationship Diagram (ERD) for Gold Layer Data Model

<img width="934" alt="image" src="https://github.com/user-attachments/assets/bf3a8e09-56fd-48e7-91d9-1fff20ad2d80" />





