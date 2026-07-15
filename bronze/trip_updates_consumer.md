## 📥 Trip Updates Consumer

### Purpose:

The Trip Updates Consumer subscribes to the Kafka topic trip_updates and stores incoming trip-level events as raw Bronze datasets for reliable downstream processing.

By persisting append-only JSON files, the consumer creates a replayable historical record that supports data lineage, fault recovery, and scalable Spark transformations.

---

## Key Responsibilities

* Consumes trip update events from the Kafka topic trip_updates.
* Extracts key trip attributes including route ID, trip ID, and stop update counts.
* Buffers incoming events using configurable batch-size and time-based flushing.
* Persists append-only JSON files within the Bronze layer.
* Preserves raw event integrity without introducing business logic.
* Enables downstream analytics for delays, service disruptions, and trip performance monitoring.

---

## Pipeline Role

The Trip Updates Consumer acts as the Bronze persistence service for trip-level operational events. By efficiently batching and storing streaming data, it creates a durable historical dataset that supports replayability, fault tolerance, and reliable downstream transformations while complementing vehicle position data to provide a comprehensive operational view of the transit network.
---

## Code:

```python
from confluent_kafka import Consumer
from google.transit import gtfs_realtime_pb2
import json
import time
import os
from datetime import datetime

# -----------------------
# Kafka Consumer Config
# -----------------------
consumer_conf = {
    "bootstrap.servers": "localhost:9092",
    "group.id": "trip-updates-bronze-consumer",
    "auto.offset.reset": "earliest"
}

consumer = Consumer(consumer_conf)
consumer.subscribe(["trip_updates"])

print("🚦 Trip Updates Bronze Consumer started...")

# -----------------------
# Output path
# -----------------------
OUTPUT_DIR = "C:/real-time-transport-analytics/Data/bronze/trip_updates"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# -----------------------
# Batch control
# -----------------------
BATCH = []
BATCH_SIZE = 100          # messages
FLUSH_INTERVAL = 5        # seconds
last_flush_time = time.time()

# -----------------------
# Consume loop
# -----------------------
while True:
    msg = consumer.poll(7.0)

    if msg is None:
        pass
    elif msg.error():
        print("❌ Consumer error:", msg.error())
    else:
        try:
            entity = gtfs_realtime_pb2.FeedEntity()
            entity.ParseFromString(msg.value())

            if entity.HasField("trip_update"):
                trip = entity.trip_update.trip

                record = {
                    "trip_id": trip.trip_id,
                    "route_id": trip.route_id,
                    "num_stop_updates": len(entity.trip_update.stop_time_update),
                    "event_ts": datetime.utcnow().isoformat()
                }

                BATCH.append(record)

        except Exception as e:
            print("⚠️ Skipping invalid trip update:", e)

    # -----------------------
    # Flush batch to disk
    # -----------------------
    if (
        len(BATCH) >= BATCH_SIZE or
        time.time() - last_flush_time >= FLUSH_INTERVAL
    ):
        if BATCH:
            filename = f"trip_updates_{int(time.time())}.json"
            filepath = os.path.join(OUTPUT_DIR, filename)

            with open(filepath, "w") as f:
                json.dump(BATCH, f, indent=2)

            print(f"✅ Saved {len(BATCH)} trip updates → {filename}")

            BATCH.clear()
            last_flush_time = time.time()
```
---

Output:
<img width="1920" height="1080" alt="Trip_updates_c" src="https://github.com/user-attachments/assets/747168b0-2991-41ab-8758-4e5aec5ead39" />
