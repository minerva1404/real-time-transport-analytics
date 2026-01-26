### Trip Updates Consumer 📥

The **Trip Updates Consumer** processes trip-level events from Kafka and stores them as raw Bronze data for replayable stream processing.

Key responsibilities:
- Consumes trip update messages from the `trip_updates` Kafka topic.
- Extracts key trip attributes such as route ID, trip ID, and stop update counts.
- Batches messages efficiently before persisting them to Bronze storage.
- Writes append-only JSON files to maintain a reliable historical record.
- Enables downstream Spark jobs to analyze delays, service disruptions, and trip patterns.
- Complements vehicle position data to provide a complete operational view of the network.

Code:
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
OUTPUT_DIR = "C:/Users/Angel/OneDrive/Desktop/real-time-transport-analytics/Data/bronze/trip_updates"
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

Output:
<img width="1920" height="1080" alt="Trip_updates_c" src="https://github.com/user-attachments/assets/747168b0-2991-41ab-8758-4e5aec5ead39" />
