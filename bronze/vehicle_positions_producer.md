## 🚍 Vehicle Positions Producer

### Purpose:

The Vehicle Positions Producer serves as the entry point of the real-time streaming pipeline. It continuously polls the MBTA GTFS-Realtime API for live vehicle position updates and publishes the events to the Kafka topic vehicle_positions.

By performing minimal transformation before publishing, the producer preserves the original event structure, ensuring high-fidelity data ingestion and providing a reliable source of truth for downstream processing.

---

### Key Responsibilities

* Continuously ingests live vehicle position events from the MBTA GTFS-Realtime feed.
* Publishes serialized vehicle position messages to the Kafka topic vehicle_positions.
* Preserves the original GTFS-Realtime payload with minimal transformation.
* Implements robust error handling and automatic retry mechanisms for uninterrupted ingestion.
* Supports continuous event streaming for downstream Bronze consumers.
* Establishes the foundation for vehicle tracking, operational monitoring, and analytical workloads.

---

### Pipeline Role

The Vehicle Positions Producer acts as the streaming ingestion service of the pipeline. It bridges the external GTFS-Realtime API with Apache Kafka, ensuring that every vehicle position update is captured and delivered reliably. By maintaining a continuous stream of raw events, it enables scalable, fault-tolerant downstream processing throughout the Medallion Architecture.

---
### Code:

```python code:
from confluent_kafka import Producer
import requests
from google.transit import gtfs_realtime_pb2
import time

producer = Producer({
    "bootstrap.servers": "localhost:9092"
})

TOPIC = "vehicle_positions"
URL = "https://cdn.mbta.com/realtime/VehiclePositions.pb"

print("🚍 Vehicle Positions Producer started...")

while True:
    try:
        response = requests.get(URL, timeout=10)

        feed = gtfs_realtime_pb2.FeedMessage()
        feed.ParseFromString(response.content)

        sent = 0
        for entity in feed.entity:
            if entity.HasField("vehicle"):
                producer.produce(
                    TOPIC,
                    value=entity.vehicle.SerializeToString()
                )
                sent += 1

        producer.flush()

        if sent > 0:
            print(f"✅ Sent {sent} vehicle position events")
        else:
            print("⚠️ No vehicle entities in feed")

        time.sleep(5)

    except Exception as e:
        print("❌ Producer error:", e)
        time.sleep(10)
```
---

## Output:
<img width="1920" height="1080" alt="vehicle_position_p" src="https://github.com/user-attachments/assets/9c170e7b-d62b-422c-b62d-2bacc2cce68e" />
