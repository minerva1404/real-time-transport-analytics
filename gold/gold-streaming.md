### Gold Layer — Analytics-Ready Streaming 

The **Gold layer** consolidates and enriches cleaned Silver streams into **analytics-ready Delta tables** optimized for reporting and decision-making.

Key responsibilities:
- Reads cleaned vehicle and trip streams continuously using **Spark Structured Streaming**.
- Merges multiple Silver datasets into a unified, query-friendly schema.
- Adds ingestion timestamps, time-zone normalization, and temporal attributes (hour, AM/PM, day of week).
- Writes append-only, fault-tolerant outputs to **Delta Lake** with checkpointing.
- Supports real-time and historical analytics without reprocessing raw data.
- Serves as the single source powering **Power BI dashboards and KPIs**.

Code:
```python
from delta import configure_spark_with_delta_pip
from pyspark.sql import SparkSession, DataFrame
from pyspark.sql.functions import col, lit, current_timestamp, from_utc_timestamp, hour, dayofweek, when, expr
from pyspark.sql.types import StructType, StructField, StringType, DoubleType, IntegerType
import logging

# -----------------------------
# Logging setup
# -----------------------------
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s [%(levelname)s] %(message)s'
)
logger = logging.getLogger(__name__)

# -----------------------------
# Functions
# -----------------------------
def create_spark_session(app_name: str = "GoldCombinedMetricsStreaming") -> SparkSession:
    builder = SparkSession.builder \
        .appName(app_name) \
        .master("local[*]") \
        .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
        .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
        .config("spark.ui.showConsoleProgress", "false")
    spark = configure_spark_with_delta_pip(builder).getOrCreate()
    spark.sparkContext.setLogLevel("ERROR")
    return spark

def read_streams(spark: SparkSession, vehicle_path: str, trip_path: str) -> tuple[DataFrame, DataFrame]:
    vehicle_schema = StructType([
        StructField("id", StringType(), True),
        StructField("trip", StringType(), True),
        StructField("route", StringType(), True),
        StructField("lat", DoubleType(), True),
        StructField("lon", DoubleType(), True)
    ])
    trip_schema = StructType([
        StructField("trip_id", StringType(), True),
        StructField("route_id", StringType(), True)
    ])
    vehicle_df = spark.readStream.schema(vehicle_schema).json(vehicle_path)
    trip_df = spark.readStream.schema(trip_schema).json(trip_path)
    return vehicle_df, trip_df

def transform_streams(vehicle_df: DataFrame, trip_df: DataFrame) -> DataFrame:
    # Vehicle metrics
    vehicle_metrics = vehicle_df \
        .withColumnRenamed("id", "vehicle_id") \
        .withColumnRenamed("trip", "trip_id") \
        .withColumn("lat", col("lat").cast(DoubleType())) \
        .withColumn("lon", col("lon").cast(DoubleType()))
    # Trip metrics
    trip_metrics = trip_df \
        .withColumnRenamed("route_id", "route") \
        .withColumn("vehicle_id", lit(None).cast(StringType())) \
        .withColumn("lat", lit(None).cast(DoubleType())) \
        .withColumn("lon", lit(None).cast(DoubleType()))
    # Combine streams
    combined_df = vehicle_metrics.unionByName(trip_metrics)
    # Add ingestion time
    combined_df = combined_df.withColumn("ingestion_time", current_timestamp())
    # Berlin time conversion
    combined_df = combined_df.withColumn(
        "ingestion_time_berlin", 
        from_utc_timestamp(col("ingestion_time"), "Europe/Berlin")
    )
    # Hour of day (12-hour clock + AM/PM)
    combined_df = combined_df.withColumn(
        "hour_of_day", 
        when(hour(col("ingestion_time_berlin")) == 0, 12)
        .otherwise(
            when(hour(col("ingestion_time_berlin")) <= 12, hour(col("ingestion_time_berlin")))
            .otherwise(hour(col("ingestion_time_berlin")) - 12)
        )
    )
    combined_df = combined_df.withColumn(
        "am_pm", 
        when(hour(col("ingestion_time_berlin")) < 12, "AM").otherwise("PM")
    )
    # Day of week mapping using dictionary and expr
    day_map = {1: "Sunday", 2: "Monday", 3: "Tuesday", 4: "Wednesday", 5: "Thursday", 6: "Friday", 7: "Saturday"}
    combined_df = combined_df.withColumn(
        "day_of_week", 
        expr(
            "CASE dayofweek(ingestion_time_berlin) " + 
            " ".join([f"WHEN {k} THEN '{v}'" for k, v in day_map.items()]) + 
            " END"
        )
    )
    return combined_df

def write_stream(combined_df: DataFrame, gold_path: str, checkpoint_path: str) -> DataFrame:
    # Original Delta stream
    query = combined_df.writeStream \
        .format("delta") \
        .outputMode("append") \
        .option("path", gold_path) \
        .option("checkpointLocation", checkpoint_path) \
        .trigger(processingTime="7 seconds") \
        .start()
    # === NEW: Console output stream for live demo ===
    console_query = combined_df.writeStream \
        .format("console") \
        .outputMode("append") \
        .trigger(processingTime="7 seconds") \
        .start()
    return query # returning Delta stream query as before

# -----------------------------
# Main
# -----------------------------
def main() -> None:
    try:
        logger.info("Starting Spark session...")
        spark = create_spark_session()
        VEHICLE_PATH = "C:/Users/Angel/OneDrive/Desktop/real-time-transport-analytics/data/silver_cleaned/vehicle_positions"
        TRIP_PATH = "C:/Users/Angel/OneDrive/Desktop/real-time-transport-analytics/data/silver_cleaned/trip_updates"
        GOLD_OUTPUT_PATH = "C:/Users/Angel/OneDrive/Desktop/real-time-transport-analytics/data/gold_delta_combined"
        CHECKPOINT_PATH = "C:/Users/Angel/OneDrive/Desktop/real-time-transport-analytics/spark-checkpoints/gold_combined_metrics"
        
        logger.info("Reading streams...")
        vehicle_df, trip_df = read_streams(spark, VEHICLE_PATH, TRIP_PATH)
        logger.info("Vehicle DF Schema:")
        vehicle_df.printSchema()
        logger.info("Trip DF Schema:")
        trip_df.printSchema()
        
        logger.info("Transforming streams...")
        combined_df = transform_streams(vehicle_df, trip_df)
        
        logger.info("Writing to Gold Delta and console...")
        query = write_stream(combined_df, GOLD_OUTPUT_PATH, CHECKPOINT_PATH)
        logger.info("🚀 GOLD COMBINED DELTA STREAMING RUNNING...")
        query.awaitTermination()
    except Exception as e:
        logger.error(f"Error in streaming job: {e}")

if __name__ == "__main__":
    main()
```

Output:
<img width="1920" height="1080" alt="gold_streaming" src="https://github.com/user-attachments/assets/700abaa9-436d-4b40-a38a-3e66a9121355" />
