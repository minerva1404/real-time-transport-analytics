### Silver Alerts (Pandas – Live Terminal Dashboard) 🚨

The **Silver Alerts module** provides real-time operational visibility directly from cleaned Silver data.

Key responsibilities:
- Continuously monitors newly generated Silver batches using Pandas.
- Surfaces live insights such as high-frequency routes, trips with many stop updates, and vehicle activity.
- Displays rolling statistics and alerts directly in the terminal for quick inspection.
- Acts as a lightweight, low-latency monitoring layer alongside Spark processing.
- Demonstrates real-time observability without requiring external dashboards.
- Validates data quality and stream health before Gold aggregation.

Code:
```python
import os
import json
import glob
import pandas as pd
from time import sleep
from collections import defaultdict
from colorama import init, Fore
import platform

# Initialize colorama
init(autoreset=True)

INPUT_DIR = "C:/real-time-transport-analytics/data/silver_cleaned"
vehicle_alerts = defaultdict(int)
trip_alerts = defaultdict(int)

def clear_console():
    if platform.system() == "Windows":
        os.system("cls")
    else:
        os.system("clear")

# ----------------------------
# Load batch files
# ----------------------------
def load_new_files(seen_files):
    trip_files = set(glob.glob(os.path.join(INPUT_DIR, "trip_updates/*.json")))
    vehicle_files = set(glob.glob(os.path.join(INPUT_DIR, "vehicle_positions/*.json")))
    all_files = trip_files | vehicle_files
    new_files = all_files - seen_files
    rows = []
    for file in new_files:
        with open(file, "r") as f:
            for line in f:
                try:
                    data = json.loads(line)
                    rows.append(data)
                except json.JSONDecodeError:
                    pass  # skip invalid lines
    return rows, new_files

def display_stats(df):
    if df.empty:
        print(Fore.YELLOW + "No new alerts yet...")
        return

    # Check if columns exist
    if 'trip' in df.columns and 'route' in df.columns:
        # Top routes
        top_routes = df['route'].value_counts().head(5)
        print(Fore.CYAN + "🔴 Top routes:")
        for route, count in top_routes.items():
            print(Fore.GREEN + f"Route {route}: {count} updates")

    if 'trip_id' in df.columns and 'stop_updates' in df.columns:
        # Top trips with most stop updates
        top_trips = df.sort_values(by="stop_updates", ascending=False).head(5)
        print(Fore.CYAN + "\n🛤️ Top trips with most stop updates:")
        for _, row in top_trips.iterrows():
            print(Fore.BLUE + f"Trip {row['trip_id']}: {row['stop_updates']} stops")

    if 'trip' in df.columns:
        print(Fore.MAGENTA + "\n🚍 Alerts per trip:")
        for trip_id in df['trip'].unique():
            print(f"Trip {trip_id}: {len(df[df['trip'] == trip_id])} updates")

    if 'id' in df.columns:
        print(Fore.BLUE + "\n🚍 Alerts per vehicle:")
        for vehicle_id in df['id'].unique():
            print(f"Vehicle {vehicle_id}: {len(df[df['id'] == vehicle_id])} updates")

def main():
    seen_files = set()
    while True:
        rows, new_files = load_new_files(seen_files)
        if new_files:
            df = pd.json_normalize(rows)
            clear_console()
            print(Fore.GREEN + "🚀 Continuous Silver Alerts Live Dashboard")
            display_stats(df)
            seen_files.update(new_files)
        else:
            sleep(7)

if __name__ == "__main__":
    main()
```

Output:

<img width="1920" height="1080" alt="silver_alerts1" src="https://github.com/user-attachments/assets/539c1d35-b5ae-4f38-8fbc-7f363bf822fd" />

<img width="1920" height="1080" alt="silver_alerts2" src="https://github.com/user-attachments/assets/6ff92a94-9eaa-4bf5-a281-40829b04120c" />

