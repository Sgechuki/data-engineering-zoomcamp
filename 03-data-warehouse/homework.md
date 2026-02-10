## Module 3 Homework: Data Warehousing & BigQuery

### Setup

### Create External Table

```sql
CREATE OR REPLACE EXTERNAL TABLE `nth-weft-417606.ny_taxi_dataset.external_yellow_tripdata`
OPTIONS (
  format = 'parquet',
  uris = ['gs://nth-weft-417606-ny-taxi-bucket/rides_dataset/rides/yellow_tripdata_2024_*_parquet.parquet']
);
```

### Create Materialized Table

```sql
CREATE OR REPLACE TABLE nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned AS
SELECT * 
FROM nth-weft-417606.ny_taxi_dataset.external_yellow_tripdata;
```

---

### Question 1

**What is the count of records for the 2024 Yellow Taxi Data?**

**Answer:**
`20,332,093`

---

### Question 2

**Write a query to count the distinct number of PULocationIDs for the entire dataset on both tables.
What is the estimated amount of data read when executed on the External Table vs the Materialized Table?**

```sql
-- External table
SELECT DISTINCT pu_location_id
FROM nth-weft-417606.ny_taxi_dataset.external_yellow_tripdata;

-- Materialized table
SELECT DISTINCT pu_location_id
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned;
```

**Answer:**

* External Table: `0 MB`
* Materialized Table: `155.12 MB`

---

### Question 3

**Retrieve PULocationID from the table, then retrieve both PULocationID and DOLocationID.
Why are the estimated bytes different?**

```sql
SELECT DISTINCT pu_location_id, do_location_id
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned;
```

**Answer:**
BigQuery is a columnar database and scans only the columns requested.
Querying two columns requires reading more data than querying one column, which increases the estimated bytes processed.

---

### Question 4

**How many records have a fare_amount of 0?**

```sql
SELECT COUNT(*)
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned
WHERE fare_amount = 0;
```

**Answer:**
`8,333`

---

### Question 5

**What is the best strategy to optimize a table if queries always filter by `tpep_dropoff_datetime` and order by `VendorID`?**

```sql
CREATE OR REPLACE TABLE nth-weft-417606.ny_taxi_dataset.yellow_tripdata_partitioned_clustered
PARTITION BY DATE(tpep_dropoff_datetime)
CLUSTER BY vendor_id AS
SELECT *
FROM nth-weft-417606.ny_taxi_dataset.external_yellow_tripdata;
```

**Answer:**
Partition by `tpep_dropoff_datetime` and cluster by `VendorID`.

---

### Question 6

**Retrieve distinct VendorIDs between 2024-03-01 and 2024-03-15 (inclusive).
Compare estimated bytes for non-partitioned vs partitioned tables.**

```sql
-- Non-partitioned table
SELECT DISTINCT vendor_id
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';

-- Partitioned & clustered table
SELECT DISTINCT vendor_id
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_partitioned_clustered
WHERE DATE(tpep_dropoff_datetime) BETWEEN '2024-03-01' AND '2024-03-15';
```

**Answer:**

* Non-partitioned table: `310.24 MB`
* Partitioned table: `26.84 MB`

---

### Question 7

**Where is the data stored in the External Table?**

**Answer:**
GCP Bucket (Google Cloud Storage)

---

### Question 8

**It is best practice in BigQuery to always cluster your data.**

**Answer:**
False

---

### Question 9

**Write a `SELECT COUNT(*)` query from the materialized table.
How many bytes does it estimate will be read? Why?**

```sql
SELECT COUNT(*)
FROM nth-weft-417606.ny_taxi_dataset.yellow_tripdata_non_partitioned;
```

**Answer:**

* Estimated bytes processed: `2.72 GB`
* Reason: BigQuery must scan the entire table to count all rows.
