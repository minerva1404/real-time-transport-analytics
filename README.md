# 🚆 Real-Time Transport Analytics

## Description:
This project implements a full *end-to-end real-time transit analytics platform* using MBTA (Boston) GTFS-realtime feeds. It ingests live vehicle positions and trip updates, processes them through *Kafka, **Spark Structured Streaming, and **Delta Lake*, and provides actionable insights via a live console dashboard.

*Key features:*
- Real-time ingestion of *vehicle positions* and *trip updates*  
- Kafka-based *Bronze → Silver → Gold streaming architecture*  
- Batch and streaming transformations using *PySpark*  
- Delta Lake storage for historical and analytical queries  
- Live monitoring of vehicle/trip alerts  
- Scalable architecture for extending to other transit APIs  

---

## 🛠 Tech Stack
- *Python 3.x*  
- *Kafka* (confluent_kafka)  
- *GTFS Realtime Protocol Buffers* (google.transit.gtfs_realtime_pb2)  
- *PySpark* (Structured Streaming)  
- *Delta Lake*  
- *Pandas* & colorama for alert dashboards  
- *JSON* for intermediate storage  
- OS: Windows / Cross-platform  

---

## ⚙️ Installation & Setup
1. *Clone the repository*  
```bash
git clone <your-repo-url>
cd real-time-transit-analytics
```
2.	Create a Python virtual environment
```
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows
```
3.	Install dependencies
```
 pip install -r requirements.txt
```
4.	Start Kafka and create topics: vehicle_positions, trip_updates, silver_vehicle_positions, silver_trip_updates

⸻

▶️ Usage Examples

1️⃣ Vehicle Positions Producer

python vehicle_positions_producer.py

	•	Fetches live vehicle positions every 5s
	•	Sends events to Kafka topic vehicle_positions

2️⃣ Trip Updates Producer

python trip_updates_producer.py

	•	Fetches live trip updates every 5s
	•	Sends events to Kafka topic trip_updates

3️⃣ Bronze Consumers

python vehicle_positions_consumer.py
python trip_updates_consumer.py

	•	Consumes raw Kafka messages
	•	Saves batch JSON files for downstream processing

4️⃣ Silver Streaming

python silver_producer_consumer.py
python silver_transform.py

	•	Cleans and validates Bronze data
	•	Writes processed Silver data to disk/Kafka

5️⃣ Silver Alerts Dashboard

python silver_alerts.py

	•	Monitors Silver layer in real-time
	•	Displays top routes, trips, and alerts

6️⃣ Gold Streaming

python gold_streaming.py

	•	Combines Silver streams
	•	Writes aggregated metrics to Delta Lake
	•	Outputs unified analytics for dashboards

⸻

## 📝 Notes
•	Kafka topics must exist before running consumers

•	Batch sizes and flush intervals can be tuned

•	Designed for scalable, real-time transit analytics

•	Easily extendable to other GTFS-realtime city feeds

