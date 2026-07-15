## 📥 Vehicle Positions Consumer

### Purpose:

The Vehicle Positions Consumer subscribes to the Kafka topic vehicle_positions and persists incoming streaming events into the Bronze layer as append-only JSON files.

Its primary objective is to establish a durable raw data store that preserves every vehicle event for replayability, lineage, and downstream Spark processing.

---

## Key Responsibilities

* Consumes vehicle position events from the Kafka topic vehicle_positions.
* Parses GTFS-Realtime vehicle messages into structured JSON records.
* Buffers streaming events using configurable batch-size and time-based thresholds.
* Persists append-only JSON files to Bronze storage.
* Preserves raw event fidelity without applying business transformations.
* Supports downstream Silver transformations, operational alerts, and analytical processing.

---

## Pipeline Role

The Vehicle Positions Consumer functions as the Bronze ingestion service within the Medallion Architecture. It converts high-frequency Kafka streams into durable Bronze datasets while balancing write efficiency and processing latency through micro-batch persistence. This approach provides reliable historical storage without sacrificing real-time responsiveness.

---
## Code:

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
---

Output:
<img width="1920" height="1080" alt="vehicle_position_c" src="https://github.com/user-attachments/assets/2d23a1c5-5287-4ce0-963e-cc613f8c4218" />
