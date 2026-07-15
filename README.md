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

  
python dashboards/silver_alerts.py

