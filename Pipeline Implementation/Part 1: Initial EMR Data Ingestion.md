# **Part 1: Initial EMR Data Pipeline (source_to_landing pipeline)**

The **source_to_landing pipeline** in Azure Data Factory (ADF) orchestrates the movement and transformation of data. This pipeline is designed with a **metadata-driven architecture**, which enhances flexibility and maintainability by dynamically adapting to different data sources and targets based on external configuration files. Below is a breakdown of the pipeline structure based on the source:

## Pipeline Breakdown

1. ### Read Configuration File (Lookup Activity):  
   The pipeline begins by reading the configuration file (`configs/emr/load_config.csv`) stored in **ADLS Gen2**. This file contains metadata about the data sources and targets, such as database names, table names, load types (full or incremental), and target paths in ADLS Gen2.
   
![image](https://github.com/user-attachments/assets/6be77b6e-5e4f-4ad5-9ebf-0f1ea413f5df)

![image](https://github.com/user-attachments/assets/c6a2c016-880c-4375-bbac-c09cad32ee60)


2. ### Iterate through Each Data Source (ForEach Activity):  
   The pipeline then iterates through each row in the configuration file, processing one data source (table) at a time.

***"Processing Logic Within ForEach Activity: "***
```
@activity('lkp_EMR_configs').output.value
```
  
<img width="530" alt="image" src="https://github.com/user-attachments/assets/34cd8ff9-a077-4f6b-9a2a-a5611b0ef418" />

- **Check for Existing Data (Get Metadata Activity)**:  
   For each table, the pipeline checks if a Parquet file already exists in the **bronze layer** of ADLS Gen2. If the file exists, it is moved to an archive folder to avoid overwriting existing data.
***"The file name we will use below logic"***

  ```
  @split(item().tablename, '.')[1]
  ```
![image](https://github.com/user-attachments/assets/f068ed2d-a227-49a3-b35e-8e0847398a3d)

<img width="523" alt="image" src="https://github.com/user-attachments/assets/7781625b-928c-4bf8-a4f0-cc264cdb1326" />


- **Check Active Flag (If Condition1 Activity)**:  

The "Check Active Flag" step uses an "If Condition" activity to archive existing files in the bronze layer. This step ensures that only files associated with active data sources are moved to the archive folder.<br>
***"Condition 1: File Exists (True) => Move the file to the Archive folder"***.

```
@and(equals(activity('fileExists').output.exists,true),equals(item().is_active, '1'))
```
![image](https://github.com/user-attachments/assets/7fd24207-2995-457f-95b1-562e329aae0e)

***"Source: Container: Bronze, Path: hosa, File: encounters"***

![image](https://github.com/user-attachments/assets/2f01b90e-277f-4ae5-b93f-a0b87d89e0f6)

***"Target: Container: Bronze,File_path -Path: hosa/archive/<year>/<month>/<day> =>"***
```
@concat(item().targetpath, '/archive/',
formatDateTime(utcNow(), 'yyyy'), '/',
formatDateTime(utcNow(), '%M'), '/',
formatDateTime(utcNow(), '%d'))
```

![image](https://github.com/user-attachments/assets/896768c8-2ec9-4604-8d89-495802c23d67)


**Incorporating this step into the overall workflow, the pipeline ensures that only active data sources with existing files in the bronze layer are processed, archived, and subsequently loaded into the target tables.** This selective processing enhances data management efficiency and avoids unnecessary operations on inactive or non-existent data sources.



- **Determine Load Type (If Condition2 Activity)**:  
   The pipeline checks the `load_type` field from the configuration file to determine if a full load or an incremental load is required.

   - **Full Load: If the condition is true**: If `load_type` is `full`, a copy activity reads the entire table from the Azure SQL database and writes it as a Parquet file to the **bronze layer** in ADLS Gen2.
```
@equals(items().loadtype, 'Full')
```
![image](https://github.com/user-attachments/assets/36b6220e-e53c-4a0c-bff5-c52f2f9ce5a8)

***If “If condition” holds true => Full Load => Copy all data from the database
table. => Enter Log details in the audit table***


***Folder and File Structure***<br>

**Bronze Container:**<br>
Source Path: bronze/hosa<br>
Target Path for Data Loads: bronze/<target-path><br>
```
@concat('select *, ''',item().datasource,''' as datasource from', item().tablename)
```
![image](https://github.com/user-attachments/assets/5093c55c-fefb-4e4e-b15c-2dd65b26458c)


***Enter Full Load Log details in the audit table:***

```
Query: @concat('insert into
audit.load_logs(data_source,tablename,numberofrowscopied,watermarkcolu
mnname,loaddate) values (''',item().datasource,''',
''',item().tablename,''',''',activity('Full_Load_CP').output.rowscopied,''',''',item().
watermark,''',''',utcNow(),''')')
```
![image](https://github.com/user-attachments/assets/c18e8a0a-ad2d-4112-b859-7abbf4dee384)



   - **Incremental Load: If the condition is false**: If `load_type` is `incremental`, the pipeline first retrieves the latest load timestamp from the audit table (stored as a Delta table in Databricks). A copy activity then reads only the new or updated records from the Azure SQL Database based on a watermark column (load date) and appends them to the existing Parquet file in the bronze layer.
   - 
  ***Fetch incremental data using the last fetched date) using Lookup=> Incremental load using copy activity
     =>Enter log details in the audit table:***

```
@concat('select coalesce(cast(max(loaddate) as date),''','1900-01-01',''')
as last_fetched_date from audit.load_logs where',' data_source=''',item().datasource,'''
and tablename=''',item().tablename,'''')
```

![image](https://github.com/user-attachments/assets/ca540cbf-5295-4088-bdcc-38644e26a6a0)

***Folder and File Structure***<br>

**Bronze Container:**<br>
Source Path: bronze/hosa<br>
Target Path for Data Loads: bronze/<target-path><br>
```
@concat('select *,''',item().datasource,''' as datasource from ',item().tablename,' where ',item().watermark,' >= ''',
activity('Fetch_logs').output.firstRow.last_fetched_date,'''')
```
<img width="846" alt="image" src="https://github.com/user-attachments/assets/2644bf03-407f-4c30-b9b4-d72f76a1b840" />


![image](https://github.com/user-attachments/assets/a4783bc3-8d87-4dde-8162-8b0b4f680139)

***Enter Incremental Load Log details in the audit table:***

```
@concat('insert into audit.load_logs(data_source,tablename,numberofrowscopied,watermarkcolumnname,loaddate)
 values (''',item().datasource,''', ''',item().tablename,''',''',
activity('Incremental_Load_CP').output.rowscopied,''',''',item().watermark,''',''',utcNow(),''')')

```
![image](https://github.com/user-attachments/assets/514c21c1-405f-4837-89e2-e8e20e8c0d1a)


This is the complete pipeline:
Before running the pipeline for each activity, the “sequential” option is selected, as shown below.

![image](https://github.com/user-attachments/assets/5dc247e2-a31c-45dc-9c97-b1ca6d319880)

![image](https://github.com/user-attachments/assets/63b109f2-7abf-4734-87d0-cea29412942e)

---
## Pipeline Limitation
The current pipeline has a limitation where it processes tables sequentially rather than in parallel. 

***Current Limitation***
```
{
  "name": "ForEach_Config_Row",
  "type": "ForEach", 
  "typeProperties": {
    "items": "@activity('lkp_EMR_configs').output.value",
    "isSequential": true,
    "activities": [...]
  }
}
```


***Reason for Sequential Processing***
The pipeline is forced to be sequential because:

- The audit table has an auto-increment ID column
- Parallel processing would cause conflicts when multiple activities try to insert records simultaneously
- This auto-increment dependency requires sequential processing

- ***Solution***

Enable parallel processing in ForEach:

```
{
  "name": "ForEach_Config_Row",
  "type": "ForEach",
  "typeProperties": {
    "items": "@activity('lkp_EMR_configs').output.value",
    "isSequential": false,
    "batchCount": 5,
    "activities": [...]
  }
}
```

This change allows multiple tables to be processed simultaneously, significantly improving pipeline performance.
