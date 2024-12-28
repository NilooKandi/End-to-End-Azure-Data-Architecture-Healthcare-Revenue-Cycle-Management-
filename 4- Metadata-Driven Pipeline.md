# Metadata-Driven Pipeline

A key highlight of this project is the implementation of a **metadata-driven pipeline** approach, which makes the solution highly **scalable** and **maintainable**.

## Metadata-Driven Pipeline
The project features a dynamic, **configuration-based pipeline** that automatically handles data ingestion from multiple sources. Instead of creating individual pipelines for each data source, a single intelligent pipeline reads from a configuration file to determine:
- Which data sources to process
- How to handle each table (full or incremental loads)
- When to process the data
- Where to store the processed data

## How it Works

### Configuration Management
- Centralised configuration file (`load_config.csv`) stored in **Azure Data Lake Storage Gen2**
- Contains essential metadata for each data source:
  - Database name
  - Table name
  - Load type (full/incremental)
  - Watermark column for incremental loads
  - Target path specifications
  - Active/inactive flags for pipeline control

### Dynamic Dataset Handling
- Uses generic **SQL** and **Parquet** datasets
- Database, schema, and table information is dynamically pulled from the configuration
- Eliminates the need for separate datasets per data source
- Path information is dynamically constructed during execution

## Key Features
- Automated handling of both **full** and **incremental loads**
- Built-in **audit logging** for tracking data movement
- Intelligent **file archival** system
- Support for multiple data sources and formats
- **Error handling** and data quality checks
- Configurable processing rules via metadata

## Benefits
- Reduced **development time** for new data sources
- Consistent processing across all data loads
- Easy **maintenance** and updates
- Scalable solution for growing data needs
- Standardised error handling and logging
- Configuration changes without the need for code modification
- Single pipeline supporting multiple data sources

## Architecture Best Practices
- Separation of **configuration** from code
- **Reusable components** and datasets
- Standardised **logging** and error handling
- Flexible and maintainable design
- Scalable for future growth

This metadata-driven approach transforms the traditional **ETL** process into a more **dynamic** and **manageable** solution while maintaining flexibility and ease of maintenance.

