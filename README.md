# 🚆 Real-Time Transport Analytics

## Project Overview
This project implements a *real-time transit analytics platform* using GTFS-realtime feeds from MBTA (Massachusetts). It ingests live vehicle positions and trip updates, processes them through *Apache Kafka* and *Spark Structured Streaming*, and stores the results in *Delta Lake* for downstream analytics.  

The pipeline follows a *Bronze → Silver → Gold* architecture to ensure data quality, validation, and analytics-ready outputs, producing *operational KPIs* for vehicle utilization, route efficiency, and peak traffic patterns.

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

