### Vehicle Positions Producer 🚍

The **Vehicle Positions Producer** continuously ingests **real-time vehicle location data** from the MBTA GTFS-Realtime feed.  

Key points:
- Streams live **vehicle position events** (latitude, longitude, trip ID, route ID) into the Kafka topic `vehicle_positions`.  
- Uses a **robust producer loop** with error handling and automatic retries to ensure **reliable data ingestion**.  
- Sends raw events directly to Kafka with **minimal transformation**, preserving the **full fidelity of the original feed**.  
- Supports downstream Bronze consumers for **JSON batch storage**, enabling replayability and fault-tolerant data processing.  
- Forms the **foundation of the Bronze layer**, providing a **continuous, real-time source of truth** for vehicle tracking and analytics.  
- Enables all **upstream processing, alerts, and analytics**, making it a critical component for operational insights and monitoring.


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


Output:
<img width="1920" height="1080" alt="vehicle_position_p" src="https://github.com/user-attachments/assets/9c170e7b-d62b-422c-b62d-2bacc2cce68e" />
