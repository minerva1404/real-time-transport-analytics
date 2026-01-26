# Pipeline Architecture

This project is designed as a **real-time, layered streaming pipeline** that ingests live public transport data and incrementally transforms it into **analytics-ready insights**.

Live **MBTA GTFS-Realtime feeds** (*Trip Updates* and *Vehicle Positions*) are streamed into **Apache Kafka**, where independent **producers and consumers** decouple ingestion from downstream processing.

Raw events are first captured in the **Bronze layer** as **append-only JSON batches**, ensuring:
- replayability  
- fault tolerance  
- zero data loss  

---

## Bronze Layer — Raw Ingestion

The Bronze layer persists raw streaming events with **minimal transformation**.  
This layer acts as the system’s **source of truth** and enables safe reprocessing.

---

## Silver Layer — Cleaning & Enrichment

The **Silver layer** processes Bronze data using **Spark Structured Streaming**, applying:

- schema enforcement  
- data cleaning & validation  
- lightweight enrichment  

This layer also includes a **Pandas-based live alerting system** that surfaces operational signals directly in the terminal for real-time observability.

---

## Gold Layer — Analytics Ready

The **Gold layer** aggregates and enriches the cleaned streams and writes them to **Delta Lake**, producing data optimized for analytics and BI workloads.

Key characteristics:
- time-based aggregations  
- business-friendly fields  
- optimized storage for querying  

---

## Analytics & Consumption

The Gold outputs are consumed by **Power BI**, enabling:
- real-time monitoring  
- historical trend analysis  
- operational transport insights  

---

The diagram below illustrates the **end-to-end data flow** and the responsibilities of each layer in the pipeline.

![Pipeline_Architecture](https://github.com/user-attachments/assets/9c0c1101-b2b8-43fa-a56c-00d8ea385208)
