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

## 📊 Project Highlights

* Metric	Value
* Daily Events Processed	1K–3K
* End-to-End Latency	<10 Minutes
* Streaming Framework	Apache Kafka + Spark Structured Streaming
* Storage Layer	Delta Lake
* Pipeline Architecture	Bronze → Silver → Gold
* Data Processing	Streaming + Micro-Batch
* Domain	Public Transportation

---

## 📈 Key Business Insights

The pipeline generated several operational insights from live transit data:

* 🚍 Peak vehicle utilization reached 82.76%
* 🛣️ Highest-performing route accounted for 2.01% of total trips
* ⏰ 61.22% of all trips occurred during the busiest operating hour
* 📅 Weekend traffic represented 68.22% of overall activity
* 🌆 PM traffic accounted for 89.8% of trips compared to 10.2% during AM hours

These metrics demonstrate how streaming pipelines can provide actionable operational intelligence for transit agencies.

---

## ⚙️ Technology Stack

### Programming

* Python
* SQL
* PySpark
* Pandas

### Streaming & Messaging

* Apache Kafka
* Spark Structured Streaming

### Storage

* Delta Lake
* JSON

### Data Formats

* GTFS-Realtime Protocol Buffers
* JSON

### Engineering Concepts

* Medallion Architecture
* ETL / ELT Pipelines
* Micro-Batch Processing
* Streaming Analytics
* Data Validation
* Data Quality Engineering
* Fault Tolerance

---

## 📂 Repository Structure

real-time-transport-analytics/
├── architecture/
├── bronze/
├── silver/
├── gold/
├── dashboard/
└── README.md

---

## 🔄 Pipeline Workflow

### Bronze Layer

Raw GTFS-Realtime events are ingested from Kafka and stored without modification, preserving the original event stream for lineage and replay.

### Silver Layer

Incoming records undergo:

* Schema validation
* Null handling
* Duplicate removal
* Data cleansing
* Standardization
* Quality flag generation

This layer produces trusted, analytics-ready datasets.

### Gold Layer

The Gold layer computes business-level KPIs including:

* Route performance
* Vehicle utilization
* Peak traffic distribution
* Hourly operational metrics
* Service efficiency indicators

These datasets are optimized for dashboarding and business reporting.

---

## 🚀 Getting Started

1. Clone the Repository

git clone <repository-url>
cd real-time-transport-analytics

⸻

2. Create a Virtual Environment

python -m venv venv
#Linux / macOS
source venv/bin/activate
#Windows
venv\Scripts\activate

⸻

3. Install Dependencies

pip install -r requirements.txt

⸻

4. Start Apache Kafka

Create the following Kafka topics:

vehicle_positions
trip_updates
silver_vehicle_positions
silver_trip_updates

---

## ▶️ Running the Pipeline

Step 1 — Start Producers

python producers/vehicle_positions_producer.py
python producers/trip_updates_producer.py

Streams live GTFS-Realtime data into Kafka topics.

⸻

Step 2 — Bronze Consumers

python consumers/vehicle_positions_consumer.py
python consumers/trip_updates_consumer.py

Consumes Kafka events and stores raw Bronze datasets.

⸻

Step 3 — Silver Processing

python silver/silver_producer_consumer.py
python silver/silver_transform.py

Performs cleansing, validation, deduplication, and schema enforcement.

⸻

Step 4 — Gold Aggregation

python gold/gold_streaming.py

Computes operational KPIs and writes analytics-ready Delta Lake tables.

⸻

Step 5 — Dashboard

python dashboards/silver_alerts.py

Visualizes operational metrics and service alerts in near real time.

---

## 💡 Engineering Decisions

### Why Apache Kafka?

Kafka enables scalable, decoupled event streaming between producers and consumers while supporting fault-tolerant message delivery.

### Why Spark Structured Streaming?

Structured Streaming provides scalable micro-batch processing with checkpointing and exactly-once processing semantics.

### Why Delta Lake?

Delta Lake ensures ACID-compliant storage, reliable streaming writes, schema enforcement, and future support for time travel.

### Why Medallion Architecture?

Separating Bronze, Silver, and Gold layers simplifies maintenance, improves data quality, and produces reliable analytical datasets.

---

## 🔮 Future Enhancements

* Docker Compose deployment
* Apache Airflow orchestration
* Cloud-native deployment on AWS
* CI/CD using GitHub Actions
* Grafana monitoring
* Data quality testing framework
* Automated alerting
* Real-time anomaly detection using machine learning

---

## 📄 License

This project is intended for educational and portfolio purposes.

⸻

⭐ If you found this project interesting, consider giving the repository a star.

