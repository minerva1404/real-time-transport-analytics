## 🥈 Silver Layer
---
### Overview

The Silver layer transforms raw Bronze data into trusted, analytics-ready datasets through validation, cleansing, standardization, and lightweight enrichment.

Unlike the Bronze layer, which preserves the original event stream, the Silver layer improves data quality while maintaining the integrity of the underlying transit events. It serves as the bridge between raw ingestion and business analytics by ensuring that downstream consumers operate on clean, consistent, and reliable data.

This layer combines Apache Kafka, Spark Structured Streaming, and Pandas to create a scalable processing pipeline capable of supporting continuous analytics and operational monitoring.

---

### 🎯 Objectives

* Transform raw Bronze events into structured datasets.
* Validate incoming records using predefined schemas.
* Remove incomplete or invalid records.
* Standardize event formats for downstream processing.
* Generate trusted datasets for Gold aggregation.
* Provide real-time operational visibility through live monitoring.

--- 

### 🔄 Silver Layer Workflow

Bronze Layer\
      ↓\
Silver Producer & Consumer\
      ↓\
Silver Landing Zone\
      ↓\
Spark Structured Streaming\
      ↓\
Silver Cleaned Storage\
|\
|-->Live Alerts\ 
|-->Gold Layer

---

## 🔁 Component 1 — Silver Producer & Consumer

### Purpose

The Silver Producer & Consumer acts as the orchestration layer between Bronze ingestion and Silver transformation. It consumes raw vehicle position and trip update events from Bronze Kafka topics, converts them into structured JSON records, republishes them to dedicated Silver Kafka topics, and persists intermediate datasets for Spark processing.

This separation improves scalability, enables independent processing stages, and reduces coupling between ingestion and transformation.

### Key Responsibilities

* Consumes raw vehicle position and trip update events from Bronze Kafka topics.
* Converts GTFS-Realtime messages into structured JSON records.
* Publishes cleaned events to dedicated Silver Kafka topics.
* Persists batched intermediate datasets to the Silver landing zone.
* Decouples ingestion from downstream transformations.
* Enables reliable and scalable stream processing.

### Pipeline Role

The Silver Producer & Consumer serves as the transition layer between raw ingestion and structured transformation. It standardizes streaming events before they enter Spark Structured Streaming, ensuring downstream processing receives consistent, organized input while maintaining efficient batch-based persistence.

Source Code:
```bash
silver_producer_consumer.py
```
Console Output:

(Insert screenshot here.)

---

## ✨ Component 2 — Silver Transformation

### Purpose

The Silver Transformation module performs continuous cleansing, validation, and enrichment using Spark Structured Streaming. Incoming datasets are validated against predefined schemas, incomplete records are removed, and standardized outputs are written to the cleaned Silver storage layer.

The result is a trusted dataset that serves as the direct input for Gold-level business analytics.

### Key Responsibilities

* Reads Silver landing datasets as continuous file streams.
* Enforces schemas for vehicle and trip datasets.
* Removes invalid and incomplete records.
* Applies lightweight enrichment and event timestamp generation.
* Writes cleaned datasets using Spark Structured Streaming.
* Maintains checkpoint directories for fault recovery.

### Pipeline Role

The Silver Transformation module functions as the data quality engine of the pipeline. By combining schema enforcement, validation, and continuous processing, it produces reliable datasets suitable for business analytics while maintaining scalability through Spark Structured Streaming.

Source Code
```bash
silver_transform.py
```
Console Output:

(Insert screenshots here.)

⸻

## 🚨 Component 3 — Silver Alerts

### Purpose

The Silver Alerts module provides real-time operational monitoring directly from the cleaned Silver datasets. Using Pandas, it continuously scans newly generated batches and presents live operational statistics through an interactive terminal dashboard.

This lightweight monitoring layer enables rapid inspection of stream health without requiring external visualization platforms.

### Key Responsibilities

* Continuously monitors newly generated Silver datasets.
* Detects high-frequency routes and active vehicle streams.
* Identifies trips with the highest stop update counts.
* Displays rolling operational statistics in real time.
* Provides lightweight observability for pipeline validation.
* Verifies stream health before Gold aggregation.

### Pipeline Role

The Silver Alerts module acts as the operational observability layer of the streaming pipeline. It provides immediate feedback on incoming data quality and transit activity, enabling rapid validation of the pipeline while supporting operational monitoring during development and testing.

Source Code:
```bash
silver_alerts.py
```
Console Output:

(Insert screenshots here.)

---

## 📂 Silver Layer Output

The Silver layer produces validated, standardized, and analytics-ready datasets.

Data/
└── silver_cleaned/
    ├── vehicle_positions/
    └── trip_updates/

These datasets become the trusted input for the Gold layer, where business KPIs, aggregations, and operational metrics are generated.

---

## ✅ Silver Layer Summary

The Silver layer serves as the data quality foundation of the streaming pipeline. Through schema validation, cleansing, standardization, continuous transformation, and live operational monitoring, it converts raw transit events into trusted datasets that power reliable business analytics and downstream decision-making.
