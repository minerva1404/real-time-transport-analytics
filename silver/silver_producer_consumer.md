## 🔁 Silver Producer & Consumer

### Purpose

The Silver Producer & Consumer acts as the orchestration layer between Bronze ingestion and Silver transformation. It consumes raw vehicle position and trip update events from Bronze Kafka topics, converts them into structured JSON records, republishes them to dedicated Silver Kafka topics, and persists intermediate datasets for Spark processing.

This separation improves scalability, enables independent processing stages, and reduces coupling between ingestion and transformation.

### Key Responsibilities

* Consumes raw vehicle position and trip update events from Bronze Kafka topics.
* Converts GTFS-Realtime messages into structured JSON records.
* Publishes standardized events to dedicated Silver Kafka topics.
* Persists batched datasets to the Silver landing zone.
* Decouples raw ingestion from downstream Spark transformations.
* Enables scalable, fault-tolerant stream processing.

### Pipeline Role

The Silver Producer & Consumer serves as the transition layer between raw ingestion and structured transformation. By organizing streaming events into standardized datasets before Spark processing, it improves pipeline modularity, scalability, and fault isolation.

### Source Code:
```python
from confluent_kafka import Producer, Consumer
from google.transit import gtfs_realtime_pb2
import json
import os
import time
from datetime import datetime

# -------------------------
# Kafka Config
# -------------------------
KAFKA_BOOTSTRAP = "localhost:9092"

BRONZE_VEHICLE_TOPIC = "vehicle_positions"
BRONZE_TRIP_TOPIC = "trip_updates"

SILVER_VEHICLE_TOPIC = "silver_vehicle_positions"
SILVER_TRIP_TOPIC = "silver_trip_updates"

OUTPUT_DIR = "C:/real-time-transport-analytics/data/silver_landing"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# -------------------------
# Producer
# -------------------------
producer = Producer({"bootstrap.servers": KAFKA_BOOTSTRAP})

# -------------------------
# Consumers
# -------------------------
vehicle_consumer = Consumer({
    'bootstrap.servers': KAFKA_BOOTSTRAP,
    'group.id': 'silver_vehicle_consumer_group',
    'auto.offset.reset': 'earliest'
})
vehicle_consumer.subscribe([BRONZE_VEHICLE_TOPIC])

trip_consumer = Consumer({
    'bootstrap.servers': KAFKA_BOOTSTRAP,
    'group.id': 'silver_trip_consumer_group',
    'auto.offset.reset': 'earliest'
})
trip_consumer.subscribe([BRONZE_TRIP_TOPIC])

# -------------------------
# Batch containers
# -------------------------
VEHICLE_BATCH_SIZE = 20
TRIP_BATCH_SIZE = 20

vehicle_batch = []
trip_batch = []

# -------------------------
# Helper to save JSON batch
# -------------------------
def save_batch(topic_name, batch):
    filename = f"{OUTPUT_DIR}/{topic_name}_{datetime.utcnow().strftime('%Y%m%d_%H%M%S%f')}.json"
    with open(filename, "w") as f:
        json.dump(batch, f)
    print(f"✅ Saved batch {topic_name} → {filename}")

# -------------------------
# Main loop
# -------------------------
print("🚀 Silver Producer+Consumer (Batched) running...")

try:
    while True:
        # --------- Vehicle Messages ---------
        v_msg = vehicle_consumer.poll(7.0)
        if v_msg is not None and not v_msg.error():
            try:
                vehicle = gtfs_realtime_pb2.VehiclePosition()
                vehicle.ParseFromString(v_msg.value())
                vehicle_json = {
                    "id": vehicle.vehicle.label,
                    "trip": vehicle.trip.trip_id,
                    "route": vehicle.trip.route_id,
                    "lat": vehicle.position.latitude,
                    "lon": vehicle.position.longitude
                }

                # Produce to Silver Kafka
                producer.produce(SILVER_VEHICLE_TOPIC, key=vehicle_json["id"], value=json.dumps(vehicle_json))
                producer.flush()

                # Add to batch
                vehicle_batch.append(vehicle_json)
                if len(vehicle_batch) >= VEHICLE_BATCH_SIZE:
                    save_batch("vehicle_positions", vehicle_batch)
                    vehicle_batch.clear()
            except Exception as e:
                print("⚠️ Vehicle parse error:", e)

        # --------- Trip Messages ---------
        t_msg = trip_consumer.poll(7.0)
        if t_msg is not None and not t_msg.error():
            try:
                entity = gtfs_realtime_pb2.FeedEntity()
                entity.ParseFromString(t_msg.value())
                if entity.HasField("trip_update"):
                    trip_json = {
                        "trip_id": entity.trip_update.trip.trip_id,
                        "route_id": entity.trip_update.trip.route_id,
                        "stop_updates": len(entity.trip_update.stop_time_update)
                    }

                    # Produce to Silver Kafka
                    producer.produce(SILVER_TRIP_TOPIC, key=trip_json["trip_id"], value=json.dumps(trip_json))
                    producer.flush()

                    # Add to batch
                    trip_batch.append(trip_json)
                    if len(trip_batch) >= TRIP_BATCH_SIZE:
                        save_batch("trip_updates", trip_batch)
                        trip_batch.clear()
            except Exception as e:
                print("⚠️ Trip parse error:", e)

        time.sleep(1)  # prevent CPU overload

except KeyboardInterrupt:
    # Save remaining messages in batches before stopping
    if vehicle_batch:
        save_batch("vehicle_positions", vehicle_batch)
    if trip_batch:
        save_batch("trip_updates", trip_batch)
    print("\n🛑 Silver Producer+Consumer stopped")

finally:
    vehicle_consumer.close()
    trip_consumer.close()
```

Output:
<img width="1920" height="1080" alt="silver_pc" src="https://github.com/user-attachments/assets/14a25926-3faa-4755-926c-1902fde0e629" />

