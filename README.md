# Flights Data Pipeline – Databricks (Real-Time & Batch)

## Overview

This project demonstrates a **real-time data engineering pipeline** for processing **flights-related data** using **Databricks**, **PySpark**, and **Delta Live Tables (DLT)**.
It follows the **Medallion Architecture** (Bronze → Silver → Gold) to ensure data quality, scalability, and maintainability.

The pipeline handles:

* **Dimension tables**: Airports, Flights, Passengers
* **Fact table**: Bookings
* **Real-time streaming ingestion**
* **Incremental loading & Change Data Capture (CDC)**
* **Data quality rules** and **upserts** with surrogate keys

---

## Architecture

```
Source Data (Incremental)  
    ↓  
Bronze Layer – Raw ingestion (Structured Streaming)  
    ↓  
Silver Layer – Cleaning, CDC, quality expectations (DLT)  
    ↓  
Gold Layer – Surrogate key upserts & fact table integration  
    ↓  
Analytics / DBT Studio
```

---

## Data Model

### Dimension Tables

* **Airports**: airport\_id, name, city, country
* **Flights**: flight\_id, airline, origin, destination, date
* **Passengers**: passenger\_id, name, gender, nationality

### Fact Table

* **Bookings**: booking\_id, passenger\_id, flight\_id, airport\_id, amount, booking\_date

---

## Pipeline Layers

### 1. Bronze Layer

* **What**: Raw, incremental data ingestion.
* **How**: `Structured Spark Streaming` reads from Delta format sources in `/Volumes/.../bronze/`.
* **Why**: Preserve original data for replayability and auditing.

### 2. Silver Layer

* **What**: Clean, standardized data with CDC and quality rules.
* **How**: `Delta Live Tables` transformations:

  * Type casting and date normalization
  * Removing `_rescued_data`
  * Applying CDC on dimension tables
  * Dropping invalid records with `@dlt.expect_all_or_drop`
* **Why**: Provide trusted, analysis-ready datasets.

### 3. Gold Layer

* **What**: Analytical schema with optimized joins and upserts.
* **How**:

  * Incrementally load Silver layer dimensions
  * Upsert using **surrogate keys**
  * Merge into fact table
* **Why**: Ensure efficient queries for dashboards and reports.

---

## Key Concepts Used

* **Medallion Architecture** (Bronze → Silver → Gold)
* **Structured Streaming** for real-time ingestion
* **Delta Live Tables (DLT)** for declarative transformations
* **Change Data Capture (CDC)** for tracking updates in dimension tables
* **Data Quality Expectations** to validate incoming data
* **Surrogate Key Upserts** for dimension tables
* **Fact-Dimension Modeling** for analytics

---

## How to Run

1. Clone this repository and open it in **Databricks Workspace**.
2. Configure your **Volumes** paths for the bronze layer sources.
3. Deploy **Bronze ingestion** scripts using Databricks Jobs for real-time streaming.
4. Create a **DLT Pipeline** for the Silver layer transformations.
5. Run **Gold layer scripts** to merge dimensions and facts for analytics.

---

## Potential Extensions

* Add **Slowly Changing Dimensions Type 2** for historical tracking.
* Integrate with **BI tools** like Power BI or Tableau.
* Automate with **Databricks Workflows** for full orchestration.
* Add **unit tests** for transformations with `pytest` or `databricks-connect`.


---


