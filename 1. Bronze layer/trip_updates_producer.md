## 🚦 Trip Updates Producer

### Purpose:

The Trip Updates Producer continuously retrieves live trip update events from the MBTA GTFS-Realtime feed and streams them into the Kafka topic trip_updates.

The producer captures schedule updates, delays, and stop-level changes with minimal processing, ensuring that downstream consumers receive a complete and accurate representation of the operational transit feed.

---

## Key Responsibilities

* Retrieves live trip update events from the MBTA GTFS-Realtime API.
* Publishes serialized trip update messages to the Kafka topic trip_updates.
* Preserves the original GTFS-Realtime event structure.
* Controls polling intervals to prevent API throttling.
* Implements reliable error handling and automatic retry logic.
* Provides the streaming foundation for service reliability analytics and operational monitoring.

---

## Pipeline Role

The Trip Updates Producer serves as the real-time trip event ingestion service for the streaming pipeline. It continuously delivers operational schedule updates into Kafka, enabling downstream consumers to analyze service delays, trip progression, and transit reliability while maintaining the integrity of the original data stream.

---

## Code:

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
---

## Output:
<img width="1920" height="1080" alt="trip_updates_p" src="https://github.com/user-attachments/assets/7f05a244-9031-4d83-8cb2-3c771a8d7298" />
