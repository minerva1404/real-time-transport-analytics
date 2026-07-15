# 🚆 Real-Time Transport Analytics

End-to-End Streaming Data Pipeline using Apache Kafka, PySpark Structured Streaming & Delta Lake

A production-inspired real-time data engineering pipeline that ingests live GTFS-Realtime transit feeds, processes streaming events through a Medallion Architecture (Bronze → Silver → Gold), and delivers analytics-ready datasets for operational monitoring and decision-making.

## Project Overview

Modern public transportation systems continuously generate high volumes of vehicle positions and trip updates. Processing these events in real time is essential for monitoring fleet operations, identifying service disruptions, understanding passenger demand, and improving transit efficiency.

This project demonstrates how a scalable streaming data pipeline can transform raw GTFS-Realtime data into trusted analytical datasets using Apache Kafka, PySpark Structured Streaming, and Delta Lake.

The pipeline follows the Medallion Architecture (Bronze → Silver → Gold), progressively improving data quality through validation, cleansing, enrichment, and aggregation before serving operational KPIs for downstream analytics.

---

## 🏗️ Solution Architecture

<img width="1536" height="1024" alt="Pipeline_Architecture" src="https://github.com/user-attachments/assets/b2fb6599-7937-4a34-abb2-ee630cf65120" />


GTFS-Realtime Feed
          │
          ▼
    Kafka Producers
          │
          ▼
     Kafka Topics
          │
          ▼
PySpark Structured Streaming
          │
          ▼
 Bronze Layer (Raw Events)
          │
          ▼
 Silver Layer (Validated & Cleaned)
          │
          ▼
 Gold Layer (Business KPIs)
          │
          ▼
    Delta Lake Storage
          │
          ▼
 Operational Dashboards

 ---
## Key Achievements
- Processed *1K–3K daily vehicle events* with *<10-minute latency*.  
- Top route handled *2.01%* of total trips; peak vehicle utilization reached *82.76%*.  
- Busiest hour contained *61.22% of trips, weekend demand was **68.22%, PM traffic *89.8% vs 10.2% AM*.  
- Built *end-to-end Bronze-Silver-Gold pipeline* with *validation, deduplication, and KPI computation*.  
- Scalable and fault-tolerant architecture, extendable to other city transit feeds.

---

## Tech Stack
- *Languages & Libraries:* Python 3.x, PySpark, Pandas  
- *Streaming & Storage:* Apache Kafka, Delta Lake  
- *Data Formats:* JSON, GTFS Realtime Protocol Buffers  
- *OS:* Cross-platform  

---

## Installation

1. *Clone the repository*
	```bash
	git clone <your-repo-url>
	cd real-time-transit-analytics
 	```
 
2.	Set up a virtual environment
	```bash
	python -m venv venv
	# Activate the environment
	source venv/bin/activate  # Linux / Mac
	venv\Scripts\activate     # Windows
	```
 
3.	Install dependencies
	   ```bash
		pip install -r requirements.txt
		
    
4.	Start Kafka and create topics
	```
	vehicle_positions
	trip_updates
	silver_vehicle_positions
	silver_trip_updates
	```



## Usage

### 1️⃣ Producers

Fetch live vehicle and trip data and send to Kafka topics every 5 seconds.

python producers/vehicle_positions_producer.py
python producers/trip_updates_producer.py

### 2️⃣ Bronze Consumers

Consume raw Kafka messages and save batch JSON files for downstream processing.

python consumers/vehicle_positions_consumer.py
python consumers/trip_updates_consumer.py

### 3️⃣ Silver Processing

Clean, validate, and transform Bronze data into analytics-ready format.

python silver/silver_producer_consumer.py
python silver/silver_transform.py

### 4️⃣ Gold Aggregation

Aggregate Silver streams into Delta Lake and compute KPIs.

python gold/gold_streaming.py

### 5️⃣ Dashboards

Monitor top routes, alerts, and operational KPIs in near real-time.

python dashboards/silver_alerts.py

