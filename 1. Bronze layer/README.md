# 🥉 Bronze Layer

### Overview

The Bronze layer is the raw ingestion layer of the Medallion Architecture. It is responsible for capturing live GTFS-Realtime events exactly as they are received, preserving the original data with minimal transformation.

This layer establishes a reliable source of truth for the entire pipeline by storing immutable, replayable datasets that support downstream processing, auditing, and fault recovery.

By separating ingestion from transformation, the Bronze layer enables scalable, fault-tolerant stream processing while maintaining complete data lineage throughout the analytics pipeline.

---

### 🎯 Objectives

* Capture raw streaming events from Apache Kafka.
* Preserve the original GTFS-Realtime payload with minimal transformation.
* Create replayable datasets for downstream Spark processing.
* Maintain append-only storage for historical analysis.
* Enable fault tolerance and reliable pipeline recovery.
* Serve as the foundation for Silver layer data cleansing and enrichment.

---

### 🔄 Bronze Layer Workflow

MBTA GTFS-Realtime API\
          ↓\
 Vehicle Positions Producer\
          ↓\
     Kafka Topic
(vehicle_positions)\
          ↓\
 Vehicle Positions Consumer\
          ↓\
 Bronze JSON Storage\
──────────────────────────────────\
MBTA GTFS-Realtime API\
          ↓\
  Trip Updates Producer\
          ↓\
     Kafka Topic
   (trip_updates)\
          ↓\
  Trip Updates Consumer\
          ↓\
 Bronze JSON Storage

---

## 🚍 Component 1 — Vehicle Positions Producer

### Purpose

The Vehicle Positions Producer serves as the entry point of the real-time streaming pipeline. It continuously polls the MBTA GTFS-Realtime API for live vehicle position updates and publishes the events to the Kafka topic vehicle_positions.

By performing minimal transformation before publishing, the producer preserves the original event structure, ensuring high-fidelity data ingestion and providing a reliable source of truth for downstream processing.

### Key Responsibilities

* Continuously ingests live vehicle position events from the MBTA GTFS-Realtime feed.
* Publishes serialized vehicle position messages to the Kafka topic vehicle_positions.
* Preserves the original GTFS-Realtime payload with minimal transformation.
* Implements robust error handling and automatic retry mechanisms.
* Supports continuous event streaming for downstream Bronze consumers.
* Establishes the foundation for vehicle tracking, operational monitoring, and analytical workloads.

### Pipeline Role

The Vehicle Positions Producer acts as the streaming ingestion service of the pipeline. It bridges the external GTFS-Realtime API with Apache Kafka, ensuring every vehicle position update is captured and delivered reliably for downstream processing.

### Console Output

<img width="1920" height="1080" alt="vehicle_position_p" src="https://github.com/user-attachments/assets/9c170e7b-d62b-422c-b62d-2bacc2cce68e" />

---

## 📥 Component 2 — Vehicle Positions Consumer

### Purpose

The Vehicle Positions Consumer subscribes to the Kafka topic vehicle_positions and persists incoming streaming events into the Bronze layer as append-only JSON files.

Its primary objective is to establish a durable raw data store that preserves every vehicle event for replayability, lineage, and downstream Spark processing.

### Key Responsibilities

* Consumes vehicle position events from the Kafka topic vehicle_positions.
* Parses GTFS-Realtime vehicle messages into structured JSON records.
* Buffers events using configurable batch-size and time-based thresholds.
* Persists append-only JSON files to Bronze storage.
* Preserves raw event fidelity without business transformations.
* Supports downstream Silver transformations and analytics.

### Pipeline Role

The Vehicle Positions Consumer functions as the Bronze persistence service within the Medallion Architecture. It efficiently converts streaming Kafka events into durable raw datasets while balancing latency and write efficiency through micro-batch persistence.

### Console Output

<img width="1920" height="1080" alt="vehicle_position_c" src="https://github.com/user-attachments/assets/2d23a1c5-5287-4ce0-963e-cc613f8c4218" />

---

## 🚦 Component 3 — Trip Updates Producer

### Purpose

The Trip Updates Producer continuously retrieves live trip update events from the MBTA GTFS-Realtime feed and streams them into the Kafka topic trip_updates.

The producer captures schedule updates, delays, and stop-level changes with minimal processing, ensuring downstream consumers receive a complete and accurate representation of the operational transit feed.

### Key Responsibilities

* Retrieves live trip update events from the MBTA GTFS-Realtime API.
* Publishes serialized trip update messages to the Kafka topic trip_updates.
* Preserves the original GTFS-Realtime event structure.
* Controls polling intervals to prevent API throttling.
* Implements reliable error handling and retry logic.
* Provides the streaming foundation for service reliability analytics.

### Pipeline Role

The Trip Updates Producer serves as the real-time trip event ingestion service for the streaming pipeline. It continuously delivers operational schedule updates into Kafka while maintaining the integrity of the original transit data.

### Console Output

<img width="1920" height="1080" alt="trip_updates_p" src="https://github.com/user-attachments/assets/7f05a244-9031-4d83-8cb2-3c771a8d7298" />
⸻

## 📥 Component 4 — Trip Updates Consumer

### Purpose

The Trip Updates Consumer subscribes to the Kafka topic trip_updates and stores incoming trip-level events as raw Bronze datasets for reliable downstream processing.

By persisting append-only JSON files, the consumer creates a replayable historical record that supports data lineage, fault recovery, and scalable Spark transformations.

### Key Responsibilities

* Consumes trip update events from the Kafka topic trip_updates.
* Extracts trip ID, route ID, and stop update information.
* Buffers incoming events using configurable batch-size and time-based flushing.
* Persists append-only JSON files within the Bronze layer.
* Preserves raw event integrity without introducing business logic.
* Enables downstream analytics for delays, service disruptions, and trip performance monitoring.

### Pipeline Role

The Trip Updates Consumer acts as the Bronze persistence service for trip-level operational events. By efficiently batching and storing streaming data, it creates a durable historical dataset that supports replayability, fault tolerance, and reliable downstream transformations.

### Console Output

<img width="1920" height="1080" alt="Trip_updates_c" src="https://github.com/user-attachments/assets/747168b0-2991-41ab-8758-4e5aec5ead39" />

---

## 📂 Bronze Layer Output

The Bronze layer generates append-only JSON datasets that preserve the original GTFS-Realtime events without business transformations.

Generated Datasets

Data/\
└── bronze/\
    ├── vehicle_positions/\
    │   ├── vehicle_positions_*.json\
    │   └── ...\
    │\
    └── trip_updates/\
        ├── trip_updates_*.json\
        └── ...

These datasets become the input for the Silver layer, where records undergo schema validation, data cleansing, standardization, and enrichment before progressing toward business analytics.

---

✅ Bronze Layer Summary

The Bronze layer establishes a resilient and replayable streaming foundation by combining Apache Kafka with append-only JSON persistence. Through dedicated producers and consumers for both vehicle positions and trip updates, it captures high-frequency transit events while preserving data integrity, supporting fault recovery, and enabling scalable downstream processing within the Medallion Architecture.
