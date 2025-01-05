# Active/Inactive Flag Implementation in ADF Pipeline

The **Active/Inactive flag** implementation utilizes the `"is_active"` column in the configuration file to determine which tables should undergo data processing in the `pl_emr_src_to_landing` pipeline. This setup provides **granular control** over data ingestion, allowing selective inclusion or exclusion of tables based on the flag's value.

## Configuration File Structure

The configuration file, typically in CSV format, contains essential details for each table, including:

- **Database Name**
- **Data Source**
- **Table Name**
- **Load Type**
- **Watermark Column**
- **Target Path**
- **Active/Inactive Flag** (`"is_active"` column)

### Active/Inactive Flag Values

- **'1': Active** - The pipeline will process the table.
- **'0': Inactive** - The pipeline will skip the table.

## Pipeline Implementation

The `pl_emr_src_to_landing` pipeline leverages the **Active/Inactive flag** in the following manner:

1. **Configuration Lookup:**  
   A **Lookup activity** is used to read the configuration file, retrieving table-specific information, including the `"is_active"` flag.

2. **ForEach Iteration:**  
   A **ForEach activity** iterates over each entry (row) in the configuration, where each row represents a potential table to be processed.

3. **Flag Check:**  
   Inside the ForEach loop, an **If Condition activity** evaluates the `"is_active"` flag for the current table:

   - **Active (`item().is_active = '1'`)**:  
     The pipeline proceeds with data ingestion, including steps like:
     - Checking for existing files.
     - Archiving files if necessary.
     - Determining the load type (full or incremental).
     - Performing the corresponding data loading operations.

   - **Inactive (`item().is_active = '0'`)**:  
     The pipeline skips the ingestion process for this table and moves to the next iteration of the ForEach loop.

### Benefits of Active/Inactive Flag

- **Selective Processing:**  
  Enables precise control over which tables are ingested without modifying the core logic of the pipeline.

- **Flexibility:**  
  Facilitates adjustments to the data ingestion workflow by simply modifying the `"is_active"` flag in the configuration file, without needing to edit the pipeline itself.

- **Maintainability:**  
  Enhances the pipeline's maintainability by providing an easy-to-manage method for including or excluding tables based on the configuration file.

## Child Pipeline Execution (`pl_copy_from_emr`)

The pipeline also makes use of an **Execute Pipeline activity** within an If Condition to call a child pipeline, `pl_copy_from_emr`. This child pipeline handles the actual data copying process, determined by whether the data load is a **full load** or **incremental load**.

### How It Works:

- The **If Condition** checks if the table is active (`item().is_active = '1'`).
- If active, it proceeds with data ingestion; otherwise, it bypasses this table and moves on to the next one in the loop.
- The child pipeline ensures that the data copying logic is reusable and modular for various tables.

## Conclusion

This **Active/Inactive flag** mechanism provides a flexible and maintainable approach to control the data processing flow. By modifying the `"is_active"` flag in the configuration file, you can easily manage which tables are processed without changing the pipeline's core logic.

