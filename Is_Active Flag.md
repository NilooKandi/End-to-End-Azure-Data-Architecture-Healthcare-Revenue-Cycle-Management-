# Active/Inactive Flag Implementation in ADF Pipeline

## Previous Pipeline Challenges

**1- Monolithic Structure**

Before implementing the is_Active flag and child pipeline, we had a single pipeline handling:
```
Pipeline: pl_emr_src_to_landing
├── Lookup (Config File)
├── ForEach
    ├── Check File Exists
    ├── Archive Files
    ├── Full Load Copy
    ├── Incremental Load Copy
    └── Write Audit Logs
```
**2- Key Issue**

**Complex Control Flow**
- All tables processed regardless of need
- No way to skip specific tables
- Complex error handling

**Performance Bottlenecks**
- Sequential processing required
- Long pipeline execution times
- Resource inefficiency

**Maintenance Challenges**
- Hard to modify individual components
- Difficult to test changes
- Complex debugging process

## New Modular Approach

## 1- Child Pipeline (Query and Dataset Configuration for pl_copy_from_emr) ##

```
Pipeline: pl_copy_from_emr
├── Parameters
│   ├── Source Dataset Settings
│   │   ├── db_name
│   │   ├── schema_name
│   │   └── table_name
│   └── Sink Dataset Settings
│       ├── container: bronze
│       ├── file_path
│       └── file_name
│
└── If Condition1 (@equals(pipeline().parameters.Load_Type,'Full'))
    ├── True: Full Load Copy Activity
    │   ├── Source: Full Load Query
    │   └── Sink: Parquet Format
    └── False: Incremental Load
        ├── Fetch_logs Activity
        ├── Source: Incremental Query
        └── Sink: Parquet Format
```

![image](https://github.com/user-attachments/assets/4bd6b996-3720-4066-8d5b-43ec35df5b6b)

***Dataset Settings (Same for Both Loads)***

First, we create a new pipeline that will handle the data copying logic:

****1. Source Dataset****

```
{
  "dataset": {
    "referenceName": "generic_sql_ds",
    "type": "DatasetReference",
    "parameters": {
      "db_name": "@pipeline().parameters.database",
      "schema_name": "@split(pipeline().parameters.tablename, '.')[0]",
      "table_name": "@split(pipeline().parameters.tablename, '.')[1]"
    }
  }
}
```
****2. Sink Dataset****
```
{
  "dataset": {
    "referenceName": "generic_adls_parquet_ds",
    "type": "DatasetReference",
    "parameters": {
      "container": "bronze",
      "file_path": "@pipeline().parameters.targetpath",
      "file_name": "@split(pipeline().parameters.tablename, '.')[1]"
    }
  }
}
```

***Query Configurations***

****1. Full Load Query****
```
@concat('select *,''',pipeline().parameters.datasource,'''
 as datasource from ',pipeline().parameters.tablename)
````

****2. Incremental Load Query****
```
@concat('select *,''',pipeline().parameters.datasource,'''
as datasource from ',pipeline().parameters.tablename,' where ',
pipeline().parameters.watermark,' >= ''',
activity('Fetch_logs').output.firstRow.last_fetched_date,'''')
```



## 1- Parent Pipeline** (`pl_emr_src_to_landing`) ##

```
Pipeline: pl_emr_src_to_landing
├── Lookup Activity (Load Config)
└── ForEach (Batch: 5, Sequential: false)
    └── If Condition3 (@equals(item().is_active,'1'))
        └── Execute Pipeline (pl_copy_from_emr)
            ├── Parameters
            │   ├── Load_Type
            │   ├── database
            │   ├── tablename
            │   ├── datasource
            │   ├── targetpath
            │   └── watermark
            └── Policy (wait: true)
```
![image](https://github.com/user-attachments/assets/d5ac9cd9-6d5a-4d7d-8c7a-a55bb274cbb8)

```
Pipeline: pl_emr_src_to_landing
├── Lookup (Config File)
└── ForEach
    └── If Condition (@equals(item().is_active,'1'))
        └── Execute Child Pipeline
```
## 2-Modifying Parent Pipeline (pl_emr_src_to_landing) ##

***Pipeline Structure***
```
{
  "name": "pl_emr_src_to_landing",
  "properties": {
    "activities": [
      {
        "name": "ForEach1",
        "type": "ForEach",
        "typeProperties": {
          "isSequential": false,
          "batchCount": 5,
          "activities": [
            {
              "name": "If Condition3",
              "type": "IfCondition",
              "expression": "@equals(item().is_active,'1')",
              "activities": [
                {
                  "name": "Execute Pipeline1",
                  "type": "ExecutePipeline",
                  "dependsOn": [],
                  "policy": {
                    "wait": true
                  },
                  "typeProperties": {
                    "pipeline": {
                      "referenceName": "pl_copy_from_emr",
                      "type": "PipelineReference"
                    },
                    "parameters": {
                      "Load_Type": "@item().loadtype",
                      "database": "@item().database",
                      "tablename": "@item().tablename",
                      "datasource": "@item().datasource",
                      "targetpath": "@item().targetpath",
                      "watermark": "@item().watermark"
                    }
                  }
                }
              ]
            }
          ]
        }
      }
    ]
  }
}
```
***Key Components:***

****1- ForEach Activity****
- Set for parallel processing
- `isSequential`: false
- `batchCount`: 5

****2- If Condition (Active Check)****
- **Name**: "If Condition3"
- **Expression**: `@equals(item().is_active,'1')`

****3- Execute Pipeline Activity****
- Invokes child pipeline with parameters
- Waits for completion (`"wait": true`)


***Parameter Mapping***

```
{
  "Load_Type": "@item().loadtype",
  "database": "@item().database",
  "tablename": "@item().tablename",
  "datasource": "@item().datasource",
  "targetpath": "@item().targetpath",
  "watermark": "@item().watermark"
}
```
![image](https://github.com/user-attachments/assets/1e969200-cadd-4ebe-994d-880d0cb910d7)

## Benefits of Parameterisation

- **Flexibility**:
- Parameterisation allows you to change source or destination details without altering the pipeline structure, such as changing connection strings, file paths, or filter conditions.
- **Reduced Risk of Error**
- **Faster Deployment**
- **Reduced Maintenance Costs**
- **Faster Feature Implementation**
- **Reduced Hard Coding**
- **Resilience to Change**
- **Suitability for Diverse Deployment Scenarios**

