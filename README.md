# NYC-Taxi-Azure-Data-Engineering
Azure End to End Big Data Pipeline with Modern Data Engineering Tech Stack


## Project Overview

This project implements an end-to-end Big Data pipeline that processes **5.7+ million** New York City taxi trip records across 60 months (2020–2025) using an API pipeline. 

It demonstrates real-world scale ingestion, processing, storage, and reporting using a full Azure Data Engineering stack, optimized for parallelism, scalability, and governance.

The source data is ingested through the [NYC Taxi & Limousine Commission API](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page), processed in a multi-zone data lake architecture (Bronze, Silver, Gold), stored using **Delta Lake**, and visualized through Power BI.


## Architecture Diagram

![data_pipeline_architecture](https://github.com/user-attachments/assets/1f6cf0d7-4e96-4d87-83af-8d359526c2f8)


## Technologies Used

| Service | Purpose |
|:--------|:--------|
| **Azure Data Factory** | Scalable, parallel ingestion via API |
| **Azure Data Lake Storage Gen2** | Secure, hierarchical data storage |
| **Azure Blob Storage** | Landing zone for raw ingestion |
| **Azure Databricks** | Distributed processing & transformations |
| **Delta Lake** | Reliable, ACID-compliant data lake storage |
| **Power BI** | Business intelligence and reporting |
| **Azure Key Vault** | Secrets and credential management |
| **Azure Active Directory** | Identity and access control |


## Technical Deep Dive

### 1. Azure Data Factory (ADF) 

- **API Integration**: ADF pipelines connect to the NYC TLC API endpoint to retrieve trip data.
- **ForEach and Pipeline Orchestration**:
  - A year-wise ForEach loop dynamically calls a month ingestion pipeline.
  - Months are ingested in parallel to maximize throughput.
- **Parameterization**:
  - Pipelines dynamically pass API URLs and storage locations based on year and month.
- **Control Flows**:
  - Dynamic content generation, activity dependencies, error handling, and retries.
- **Parallel Processing**:
  - ADF’s built-in parallelism allowed ingestion across multiple years and months simultaneously, reducing overall latency.



### 2. Azure Data Lake Storage Gen2 

- **Multi-zone Lake**:
  - **Raw (Bronze)**: Direct dump of API JSON responses.
  - **Transformed (Silver)**: Cleaned, structured data after validation and cleansing.
  - **Curated (Gold)**: Aggregated, analytics-ready datasets.
- **Hierarchical Namespace**:
  - Organized folder structures.
  - Optimized for analytics workloads.


### 3. Azure Databricks 

- **Silver Notebook**:
  - Cleans raw data.
  - Handles missing values, standardizes schemas, enforces data types.
- **Gold Notebook**:
  - Aggregates trip data (e.g., total trips, revenue analysis).
  - Prepares curated datasets for BI reporting.
- **Optimized for Delta Lake**:
  - Cache & optimize reads/writes.


### 4. Delta Lake 

Key features leveraged:
- **ACID Transactions**: Guarantees write consistency even under failures.
- **Schema Enforcement and Evolution**: Ensures only valid data is written; handles schema drift gracefully.
- **Time Travel**: Ability to query previous versions of data (audit/history/debugging).
- **Data Versioning**: Automatic tracking of changes to datasets.
- **Indexes and Optimized Storage**: Faster queries through data skipping and Z-order clustering (planned for future enhancement).
- **Upserts (MERGE operations)**: Supports complex CDC (Change Data Capture) scenarios.



### 5. Power BI 

- Connects to curated Gold tables.
- Enables real-time analytics and self-service BI.
- Example Dashboards:
  - Year-over-Year trip volume analysis.
  - Monthly revenue aggregation.
  - Geographic pickup and dropoff trends.


## Repository Structure

```
NYC-Taxi-Azure-Data-Engineering/
│
├── architecture/
│   └── data_pipeline_architecture.png
│
├── notebooks/
│   ├── gold_notebook.ipynb
│   └── silver_notebook.ipynb
│
├── Azure_Data_Factory_Pipelines/
│   ├── NYC_Data_Pipeline_Year_support_live/
│   │   ├── datasets/
│   │   ├── linkedServices/
│   │   ├── pipelines/
│   │   └── dataflows/
│   │
│   └── NYC_Grab_month_Data_support_live/
│       ├── datasets/
│       ├── linkedServices/
│       ├── pipelines/
│       └── dataflows/
│
├── README.md
└── .gitignore
```


## How the Pipeline Works 

1. **Ingestion (ADF)**
   - Year-wise ForEach loop.
   - Each year triggers 12 month ingestion pipelines in parallel.
   - Data fetched through REST API and stored in Azure Blob/Raw layer.

2. **Transformation (Databricks)**
   - Silver layer cleaning: type casting, null handling, schema alignment.
   - Gold layer aggregation: summarization, windowing, enrichment.
   - Saves output as Delta tables in curated zones.

3. **Serving (Power BI)**
   - Gold tables connected directly for analytics.
   - Dashboards and KPIs created from Gold layer data.



## Security Measures

- **Azure Key Vault**: API keys and storage credentials are securely managed.
- **Azure Active Directory (AAD)**: Authentication for Databricks, ADF, and Storage.
- **Role-Based Access Control (RBAC)**: Fine-grained permission management on Azure resources.
- **Data Encryption**: Data encrypted at rest and in transit.



