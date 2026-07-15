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
&#11015;
		  
    Kafka Producers
&#11015;
		   
     Kafka Topics
&#11015;
		   
	PySpark Structured Streaming
&#11015;
		   
	Bronze Layer (Raw Events)
&#11015;
		  
	Silver Layer (Validated & Cleaned)
&#11015;
		   
	Gold Layer (Business KPIs)
&#11015;
		   
    Delta Lake Storage
&#11015;
		   
	Operational Dashboards

 ---
## ✨ Key Features

* Real-time ingestion of GTFS-Realtime vehicle positions and trip updates
* Apache Kafka–based event streaming architecture
* PySpark Structured Streaming with micro-batch processing
* Bronze → Silver → Gold Medallion Architecture
* Delta Lake storage with ACID guarantees
* Automated data validation and quality checks
* Schema enforcement and duplicate removal
* Route performance analytics
* Vehicle utilization monitoring
* Peak traffic analysis
* Analytics-ready Gold layer for BI dashboards
* Modular and fault-tolerant pipeline design

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

