# 🏗️ Pipeline Architecture

This project implements a production-inspired, real-time streaming data pipeline that ingests live public transportation events, processes them through a Medallion Architecture (Bronze → Silver → Gold), and delivers analytics-ready datasets for operational monitoring and business intelligence.

Live MBTA GTFS-Realtime feeds—including Vehicle Positions and Trip Updates—are continuously ingested through Apache Kafka, where independent producers and consumers decouple data ingestion from downstream processing. As data flows through each layer, it is progressively validated, standardized, enriched, and optimized for analytics while maintaining scalability, fault tolerance, and data lineage.

---

## 🥉 Bronze Layer — Raw Data Ingestion

The Bronze layer captures raw streaming events with minimal transformation, preserving the original GTFS-Realtime payload exactly as it is received.

This layer establishes the source of truth for the entire pipeline by creating immutable, replayable datasets that support reliable downstream processing.

### Key Characteristics

* Append-only raw JSON storage
* High-throughput Kafka event ingestion
* Replayable historical datasets
* Fault-tolerant data persistence
* Complete data lineage preservation

---

## 🥈 Silver Layer — Data Quality & Standardization

The Silver layer transforms raw Bronze datasets into trusted, analytics-ready data using Spark Structured Streaming.

Incoming events undergo validation, cleansing, schema enforcement, and lightweight enrichment before being written to standardized Silver datasets. A lightweight Pandas-based live alerting module continuously monitors the processed data, providing real-time operational visibility and validating stream health.

### Key Characteristics

* Schema enforcement
* Data validation and cleansing
* Standardization and enrichment
* Continuous stream processing
* Real-time operational monitoring

--- 

## 🥇 Gold Layer — Business Analytics

The Gold layer combines validated Silver datasets into a unified analytical model and stores the results in Delta Lake for business intelligence workloads.

Additional temporal attributes, ingestion metadata, and business-friendly fields are generated to support both real-time and historical analytics while maintaining ACID-compliant storage and fault tolerance.

### Key Characteristics

* Unified analytical data model
* Business KPI generation
* Temporal feature engineering
* Delta Lake storage
* Query-optimized analytical datasets

---

### 📊 Analytics & Business Consumption

The analytics-ready Gold datasets power Power BI dashboards, enabling transportation operators to monitor fleet performance and derive operational insights from continuously updated streaming data.

The resulting dashboards support:

* Real-time operational monitoring
* Historical trend analysis
* Vehicle utilization tracking
* Route performance analysis
* Peak traffic identification
* Operational decision-making

---

The diagram below illustrates the complete end-to-end streaming architecture and the responsibilities of each layer within the Medallion Architecture: \
![Pipeline_Architecture](https://github.com/user-attachments/assets/9c0c1101-b2b8-43fa-a56c-00d8ea385208)
