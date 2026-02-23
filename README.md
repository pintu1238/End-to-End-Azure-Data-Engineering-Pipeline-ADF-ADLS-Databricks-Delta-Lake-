# End-to-End Azure Data Engineering Pipeline

## Project Overview

This project implements a complete Lakehouse data pipeline using Azure services. It ingests raw transactional data, processes it through the Medallion architecture (Bronze → Silver → Gold), and builds analytical dimensional models using Azure Databricks and Delta Lake.

The solution demonstrates real-world data engineering practices including incremental loading, surrogate key generation, and fact–dimension modeling.

---

## Architecture

**Data Flow:**

```
Source Systems → Azure Data Factory → ADLS Gen2 (Bronze)
→ Databricks (Silver Transformations)
→ Delta Lake Gold Layer (Dimensions & Facts)
→ Analytics / BI
```

---

## Tech Stack

* Azure Data Factory (ADF)
* Azure Data Lake Storage Gen2 (ADLS)
* Azure Databricks
* PySpark
* Delta Lake
* SQL
* Medallion Architecture

---

## Repository Structure

```
azure-data-engineering-project/
 ├── notebooks/          # Databricks notebooks
 ├── adf-pipelines/      # ADF ARM templates
 ├── sql/                # SQL scripts (if any)
 ├── architecture/       # Architecture diagram
 └── README.md
```

---

## Bronze Layer

* Raw data ingested from source using ADF
* Stored in ADLS Gen2
* No transformations applied
* Append-only design

---

## Silver Layer

### Read Raw Silver Data

```sql
SELECT *
FROM parquet.`abfss://silver@cfstorageaccount9.dfs.core.windows.net/silverdata`
```

### Customer Dimension Source

```sql
SELECT DISTINCT(CustomerID) AS CustomerId,
       CustomerName,
       Country
FROM parquet.`abfss://silver@cfstorageaccount9.dfs.core.windows.net/silverdata`
```

### Product Dimension Source

```sql
SELECT DISTINCT(ProductID) AS ProductId,
       ProductName,
       ProductCategory,
       UnitPrice
FROM parquet.`abfss://silver@cfstorageaccount9.dfs.core.windows.net/silverdata`
```

### Date Dimension Source

```sql
SELECT DISTINCT(OrderDate)
FROM parquet.`abfss://silver@cfstorageaccount9.dfs.core.windows.net/silverdata`
```

**Silver Layer Responsibilities**

* Data cleansing
* Standardization
* Deduplication
* Schema enforcement

---

## Gold Layer (Dimensional Modeling)

### Customer Dimension

* Business key: CustomerID
* Surrogate key: Dim_Customer_Key
* Deduplicated records
* Stored as Delta table

---

### Product Dimension

* Business key: ProductID
* Surrogate key generated
* Includes category and pricing
* Stored in Gold layer

---

### Date Dimension

* One row per calendar date
* Supports time-based analytics
* Stored as Delta table

---

### Fact Table

The fact table is built by joining:

* Customer dimension
* Product dimension
* Date dimension
* Region dimension

**Measures included**

* Quantity
* UnitPrice
* TotalPrice

---

## Incremental Loading Strategy

The pipeline implements incremental processing using:

* ADF Lookup activities
* Watermark pattern
* Delta MERGE operations
* Idempotent loads

This ensures efficient processing of only new or changed data.

---

## Delta Lake Features Used

* ACID transactions
* MERGE (upsert)
* Schema enforcement
* Scalable storage on ADLS

---

## Key Highlights

* End-to-end Azure data pipeline
* Medallion architecture implementation
* Incremental data loading
* Surrogate key generation
* Star schema modeling
* Delta Lake upserts
* Production-style pipeline design

---

## How to Run the Project

1. Run ADF pipeline to ingest data into Bronze
2. Execute Databricks notebooks for Silver transformations
3. Run Gold layer notebooks to build dimensions and facts
4. Query Delta tables for analytics

---

## Business Value

This pipeline enables:

* Scalable analytics
* Historical tracking
* Efficient incremental processing
* BI-ready dimensional model

---

## Future Enhancements

* Implement SCD Type 2
* Add orchestration monitoring
* Integrate Power BI dashboard
* Implement data quality checks

---

## Author

Vicky (Pintu)
Aspiring Data Engineer | Azure | PySpark | Delta Lake

---

## Resume Impact Statement

Built an end-to-end Azure Data Engineering pipeline using ADF, ADLS Gen2, Databricks, and Delta Lake implementing Medallion architecture, incremental loading, and star schema modeling.
