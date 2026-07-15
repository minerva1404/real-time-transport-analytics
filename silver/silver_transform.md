## ✨ Silver Transformation

### Purpose

The Silver Transformation module performs continuous cleansing, validation, and enrichment using Spark Structured Streaming. Incoming datasets are validated against predefined schemas, incomplete records are removed, and standardized outputs are written to the cleaned Silver storage layer.

The resulting datasets provide a trusted analytical foundation for Gold-level aggregation and business intelligence.

### Key Responsibilities

* Reads Silver landing datasets as continuous file streams.
* Enforces schemas for vehicle and trip datasets.
* Removes incomplete and invalid records.
* Applies lightweight enrichment and event timestamp generation.
* Writes cleaned datasets using Spark Structured Streaming.
* Maintains checkpoint directories for fault recovery.

### Pipeline Role

The Silver Transformation module functions as the data quality engine of the pipeline. Through continuous validation and schema enforcement, it produces reliable datasets that support scalable analytics while preserving fault tolerance using Spark checkpointing.

### Source Code:
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, from_json, lit, concat, hour, dayofweek, current_timestamp, unix_timestamp
from pyspark.sql.types import StructType, StructField, StringType, DoubleType, IntegerType, LongType

# --------------------------------------------------
# Spark Session
# --------------------------------------------------
spark = SparkSession.builder \
    .appName("SilverTransformer") \
    .master("local[*]") \
    .getOrCreate()
spark.sparkContext.setLogLevel("WARN")

# --------------------------------------------------
# Paths
# --------------------------------------------------
INPUT_DIR = "C:/real-time-transport-analytics/data/silver_landing"
OUTPUT_DIR = "C:/real-time-transport-analytics/data/silver_cleaned"
CHECKPOINT_DIR = "C:/real-time-transport-analytics/spark-checkpoints/silver_transformer"

# --------------------------------------------------
# Schemas
# --------------------------------------------------
vehicle_schema = StructType([
    StructField("id", StringType(), True),
    StructField("trip", StringType(), True),
    StructField("route", StringType(), True),
    StructField("lat", DoubleType(), True),
    StructField("lon", DoubleType(), True)
])

trip_schema = StructType([
    StructField("trip_id", StringType(), True),
    StructField("route_id", StringType(), True),
    StructField("stop_updates", IntegerType(), True)
])

# --------------------------------------------------
# Read JSON as Stream
# --------------------------------------------------
vehicle_df = spark.readStream \
    .schema(vehicle_schema) \
    .option("maxFilesPerTrigger", 20) \
    .json(f"{INPUT_DIR}/vehicle_positions_*.json")

trip_df = spark.readStream \
    .schema(trip_schema) \
    .option("maxFilesPerTrigger", 20) \
    .json(f"{INPUT_DIR}/trip_updates_*.json")

# --------------------------------------------------
# Transform
# --------------------------------------------------
vehicle_clean = vehicle_df \
    .dropna(subset=["id", "trip", "route", "lat", "lon"]) \
    .withColumn("event_ts", unix_timestamp(current_timestamp()) * 1000)

trip_clean = trip_df \
    .dropna(subset=["trip_id", "route_id", "stop_updates"])

# --------------------------------------------------
# Write to Silver Cleaned
# --------------------------------------------------
def process_batch(df, epoch_id, topic):
    if df.isEmpty():
        return
    print(f"\n=== SILVER BATCH {epoch_id} ({topic}) ===")
    df.show()
    df.coalesce(2).write.mode("append").json(f"{OUTPUT_DIR}/{topic}")

vehicle_clean.writeStream \
    .foreachBatch(lambda df, epoch_id: process_batch(df, epoch_id, "vehicle_positions")) \
    .outputMode("append") \
    .option("checkpointLocation", f"{CHECKPOINT_DIR}/vehicle") \
    .trigger(processingTime="10 seconds") \
    .start()

trip_clean.writeStream \
    .foreachBatch(lambda df, epoch_id: process_batch(df, epoch_id, "trip_updates")) \
    .outputMode("append") \
    .option("checkpointLocation", f"{CHECKPOINT_DIR}/trip") \
    .trigger(processingTime="10 seconds") \
    .start()

spark.streams.awaitAnyTermination()
```

Output:
<img width="1920" height="1080" alt="silver_transform2" src="https://github.com/user-attachments/assets/87567120-810c-4ad9-beaf-5ac8782c6d69" />

<img width="1920" height="1080" alt="silver_transform1" src="https://github.com/user-attachments/assets/2f4794de-5905-4b23-9d72-4c4e46645727" />

<img width="1920" height="1080" alt="silver_transform3" src="https://github.com/user-attachments/assets/0082ff5e-ae61-48d6-851c-2512b6bd54b9" />
