### Trip Updates Producer 🚦

The **Trip Updates Producer** ingests live trip-level operational data from the MBTA GTFS-Realtime feed, including delays, schedule changes, and stop-level updates.

Key responsibilities:
- Streams real-time **trip update events** into the Kafka topic `trip_updates`.
- Extracts GTFS trip update entities with minimal transformation.
- Publishes events at a controlled polling interval to avoid API throttling.
- Preserves the full structure of the original feed for downstream processing.
- Provides the foundation for **service reliability analysis** and operational monitoring.

Code:
```python
from confluent_kafka import Producer
import requests
from google.transit import gtfs_realtime_pb2
import time

producer = Producer({
    "bootstrap.servers": "localhost:9092"
})

TOPIC = "trip_updates"
URL = "https://cdn.mbta.com/realtime/TripUpdates.pb"

print("🚦 Trip Updates Producer started...")

while True:
    try:
        response = requests.get(URL, timeout=10)

        feed = gtfs_realtime_pb2.FeedMessage()
        feed.ParseFromString(response.content)

        trip_entities = [
            e for e in feed.entity if e.HasField("trip_update")
        ]

        if trip_entities:
            for entity in trip_entities:
                producer.produce(
                    TOPIC,
                    value=entity.SerializeToString()
                )
            producer.flush()
            print(f"📤 Sent {len(trip_entities)} trip update events")
        else:
            print("⚠️ No trip_update entities in feed")

        time.sleep(5)

    except Exception as e:
        print("❌ Producer error:", e)
        time.sleep(10)
```

Output:
<img width="1920" height="1080" alt="trip_updates_p" src="https://github.com/user-attachments/assets/7f05a244-9031-4d83-8cb2-3c771a8d7298" />
