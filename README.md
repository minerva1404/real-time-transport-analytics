# Real-Time MBTA Transport Analytics

End-to-end real-time transport analytics pipeline built on live **MBTA GTFS-Realtime feeds**, designed to demonstrate production-style **streaming ingestion, transformation, alerting, and analytics**.

This project processes live **Trip Updates** and **Vehicle Positions** data using Kafka, Spark Structured Streaming, and Delta Lake, and surfaces insights through a Power BI dashboard.

---

## Architecture & Data Flow

The system follows a layered **Bronze → Silver → Gold** streaming architecture:

- **GTFS-Realtime APIs** → Kafka Producers  
- **Kafka Consumers** → Bronze (raw JSON batch storage)  
- **Spark Structured Streaming** → Silver (cleaned & enriched data)  
- **Delta Lake** → Gold (analytics-ready tables)  
- **Power BI** → Visualization & KPIs  

Architecture and data flow diagrams are included in the `architecture/` folder.

---

## Bronze Layer – Real-Time Kafka Ingestion

The Bronze layer focuses on **raw, lossless ingestion** of live MBTA data.

**Components**
- Trip Updates Producer → streams live trip changes to Kafka
- Vehicle Positions Producer → streams live vehicle locations to Kafka
- Trip Updates Consumer → batches and persists raw trip data as JSON
- Vehicle Positions Consumer → batches and persists raw vehicle data as JSON

**Output**
- Append-only JSON files for replayability and downstream processing
- Live terminal logs confirming streaming activity



---

## Silver Layer – Cleaning, Enrichment & Alerts

The Silver layer standardizes and enriches raw data.

**Processing**
- Kafka-based Silver producer + consumer for structured handoff
- Spark Structured Streaming for schema enforcement and cleaning
- Cleaned streaming output written as structured JSON

**Operational Alerts**
- Pandas-based live alerting layer
- Real-time terminal dashboard highlighting high-activity routes, trips, and vehicles



---

## Gold Layer – Analytics-Ready Streaming Output

The Gold layer combines cleaned streams into a **Delta Lake table** optimized for analytics.

**Features**
- Unified vehicle + trip stream
- Time-based enrichment (hour, AM/PM, day of week)
- Continuous streaming writes to Delta
- Live console output for real-time verification

Gold outputs are stored in Delta format and used directly by BI tools.

---

## Power BI Dashboard

The final Gold dataset feeds a Power BI dashboard showcasing:
- Hourly trip distribution
- Route-level activity trends
- Vehicle movement patterns



---

## Skills Demonstrated

- Real-time streaming with Kafka (producers & consumers)
- Spark Structured Streaming (file-based & Delta streaming)
- Bronze / Silver / Gold data architecture
- Delta Lake for analytics-ready storage
- Python (Kafka, Spark, Pandas)
- Operational alerting and BI visualization
