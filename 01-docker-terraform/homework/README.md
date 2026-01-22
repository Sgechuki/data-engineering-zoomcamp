# Homework 1: Docker, SQL and Terraform for Data Engineering Zoomcamp 2026


## 📦 Question 1: pip Version in `python:3.13`

**Goal:** Verify the pip version bundled with the official Python image.

### 🔧 Command

```bash
docker run -it --rm --entrypoint=bash python:3.13
pip --version
```

### ✅ Answer

```
pip 25.3
```

---

## 🐘 Question 2: Postgres Hostname & Port (Docker Compose)

**Goal:** Determine how pgAdmin should connect to Postgres when using Docker Compose.

### 🔑 Key Rule

Inside Docker Compose, **containers communicate using service names**, not `localhost`.

### ✅ Correct Connection Details

```
Host: db
Port: 5432
```

---

## 📊 Question 3: Trips ≤ 1 Mile (Nov 2025)

**Goal:** Count short trips (≤ 1 mile) in November 2025.

### 🧠 SQL Query

```sql
SELECT COUNT(1)
FROM green_tripdata_2025_11
WHERE trip_distance <= 1
  AND lpep_pickup_datetime >= '01-NOV-2025'
  AND lpep_pickup_datetime < '01-DEC-2025';
```

### ✅ Answer

```
8,007 trips
```

---

## 📏 Question 4: Pickup Day With Longest Trip (< 100 miles)

**Goal:** Identify the pickup day that had the longest trip (excluding outliers).

### 🧠 SQL Query

```sql
WITH longest_trip AS (
    SELECT MAX(trip_distance) AS long_trip
    FROM green_tripdata_2025_11
    WHERE trip_distance < 100
)
SELECT lpep_pickup_datetime
FROM green_tripdata_2025_11
WHERE trip_distance = (SELECT long_trip FROM longest_trip);
```

### ✅ Answer

```
2025-11-14
```

---

## 💰 Question 5: Pickup Zone With Highest Total Amount (Nov 18, 2025)

**Goal:** Find which pickup zone generated the most revenue on a specific day.

### 🧠 SQL Query

```sql
SELECT z."Zone", SUM(t.total_amount) AS total_amount
FROM green_tripdata_2025_11 t
LEFT JOIN taxi_zone_lookup z
  ON z."LocationID" = t."PULocationID"
WHERE DATE_TRUNC('day', lpep_pickup_datetime) = '18-NOV-2025'
GROUP BY z."Zone"
ORDER BY total_amount DESC
LIMIT 1;
```

### ✅ Answer

```
East Harlem North
```

---

## 🎯 Question 6: Drop-off Zone With Largest Tip (From East Harlem North)

**Goal:** For passengers picked up in **East Harlem North**, identify where the highest tip was paid.

### 🧠 SQL Query

```sql
SELECT dz."Zone", t.tip_amount
FROM green_tripdata_2025_11 t
LEFT JOIN taxi_zone_lookup pz ON pz."LocationID" = t."PULocationID"
LEFT JOIN taxi_zone_lookup dz ON dz."LocationID" = t."DOLocationID"
WHERE pz."Zone" = 'East Harlem North'
ORDER BY t.tip_amount DESC
LIMIT 1;
```

### ✅ Answer

```
Yorkville West
```

---

👤 **Author:** Seth Gechuki
📅 **Dataset:** NYC Green Taxi – November 2025
