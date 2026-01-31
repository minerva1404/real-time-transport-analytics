### Vehicle Positions Consumer 📥

The **Vehicle Positions Consumer** reads real-time vehicle events from the Kafka topic `vehicle_positions` and persists them into the **Bronze layer** as batched JSON files.

Key responsibilities:
- Consumes high-frequency vehicle location streams using a dedicated Kafka consumer group.
- Parses GTFS-Realtime vehicle messages into structured JSON records.
- Batches incoming events based on **size and time thresholds** to balance latency and write efficiency.
- Writes append-only JSON files to Bronze storage, ensuring **fault tolerance and replayability**.
- Acts as the **raw persistence layer** for all downstream Spark processing, alerts, and analytics.
- Designed to handle noisy, high-volume feeds without data loss or backpressure issues.

```python code
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
    "group.id": "vehicle-positions-bronze-consumer",
    "auto.offset.reset": "earliest"
}

consumer = Consumer(consumer_conf)
consumer.subscribe(["vehicle_positions"])

print("🚍 Vehicle Positions Bronze Consumer started...")

# -----------------------
# Output path
# -----------------------
OUTPUT_DIR = "C:/real-time-transport-analytics/Data/bronze/vehicle_positions"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# -----------------------
# Batch control
# -----------------------
BATCH = []
BATCH_SIZE = 150          # vehicle feed is noisy → slightly bigger batch
FLUSH_INTERVAL = 5        # seconds
last_flush_time = time.time()

# -----------------------
# Consume loop
# -----------------------
while True:
    msg = consumer.poll(1.0)

    if msg is None:
        pass
    elif msg.error():
        print("❌ Consumer error:", msg.error())
    else:
        try:
            vehicle = gtfs_realtime_pb2.VehiclePosition()
            vehicle.ParseFromString(msg.value())

            record = {
                "vehicle_id": vehicle.vehicle.label,
                "trip_id": vehicle.trip.trip_id,
                "route_id": vehicle.trip.route_id,
                "latitude": vehicle.position.latitude,
                "longitude": vehicle.position.longitude,
                "event_ts": datetime.utcnow().isoformat()
            }

            BATCH.append(record)

        except Exception as e:
            print("⚠️ Skipping invalid vehicle message:", e)

    # -----------------------
    # Flush batch to disk
    # -----------------------
    if (
        len(BATCH) >= BATCH_SIZE or
        time.time() - last_flush_time >= FLUSH_INTERVAL
    ):
        if BATCH:
            filename = f"vehicle_positions_{int(time.time())}.json"
            filepath = os.path.join(OUTPUT_DIR, filename)

            with open(filepath, "w") as f:
                json.dump(BATCH, f, indent=2)

            print(f"✅ Saved {len(BATCH)} vehicle positions → {filename}")

            BATCH.clear()
            last_flush_time = time.time()
```

Output:
<img width="1920" height="1080" alt="vehicle_position_c" src="https://github.com/user-attachments/assets/2d23a1c5-5287-4ce0-963e-cc613f8c4218" />
