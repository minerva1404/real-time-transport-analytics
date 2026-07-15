## 🥇 Gold Layer

### Overview

The Gold layer represents the business consumption layer of the Medallion Architecture. It transforms validated Silver datasets into analytics-ready Delta Lake tables that support reporting, dashboarding, and operational decision-making.

Unlike the Silver layer, which focuses on data quality, the Gold layer emphasizes business value by combining multiple streaming datasets, enriching them with temporal attributes, and producing query-optimized outputs for downstream analytical workloads.

This layer serves as the final stage of the streaming pipeline and powers real-time business intelligence through continuously updated Delta tables.

---

### 🎯 Objectives

* Consume trusted Silver datasets.
* Merge multiple streaming sources into a unified analytical model.
* Generate business-ready datasets.
* Enrich records with temporal dimensions.
* Persist analytics-ready Delta Lake tables.
* Provide reliable data for dashboards and reporting.

---

### 🔄 Gold Layer Workflow

Silver Cleaned Data\
        ↓\
Spark Structured Streaming\
        ↓\
Business Transformations\
        ↓\
Delta Lake\
        ↓\
Power BI Dashboard\
        ↓\
Business KPIs\

---

## 🥇 Component — Gold Streaming Analytics

## Purpose

The Gold Streaming Analytics module continuously consumes validated Silver datasets, combines vehicle and trip streams into a unified analytical model, enriches records with business-friendly temporal attributes, and writes analytics-ready Delta Lake tables for reporting and decision-making.

The resulting datasets provide a single, trusted source of operational metrics that can be queried efficiently without reprocessing raw streaming events.

## Key Responsibilities

* Continuously reads validated Silver datasets using Spark Structured Streaming.
* Combines vehicle and trip streams into a unified analytical schema.
* Generates ingestion timestamps and temporal dimensions.
* Performs time-zone normalization for business reporting.
* Writes append-only Delta Lake tables with checkpointing.
* Supports both real-time analytics and historical reporting.
* Powers downstream Power BI dashboards and operational KPIs.

## Pipeline Role

The Gold Streaming Analytics module serves as the business intelligence layer of the streaming pipeline. It transforms trusted operational datasets into analytics-ready tables optimized for reporting, dashboarding, and decision-making while maintaining fault tolerance through Delta Lake and Spark checkpointing.

Console Output

<img width="1920" height="1080" alt="gold_streaming" src="https://github.com/user-attachments/assets/700abaa9-436d-4b40-a38a-3e66a9121355" />

⸻

## 📂 Gold Layer Output

The Gold layer produces analytics-ready Delta Lake tables optimized for business intelligence workloads.

Data/
└── gold_delta_combined/

These datasets serve as the primary source for Power BI dashboards, operational reporting, and future machine learning workloads.



✅ Gold Layer Summary

The Gold layer transforms trusted Silver datasets into business-ready analytical assets by combining multiple streaming sources, enriching records with temporal dimensions, and storing them as fault-tolerant Delta Lake tables. As the final stage of the Medallion Architecture, it delivers reliable, continuously updated datasets for real-time reporting, dashboarding, and operational decision-making.
