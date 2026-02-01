✨ **Workflow orchestration with Kestra & ETL pipelines** ✨  

This document captures answers and SQL checks from the NYC Taxi ETL workflow 🚕📦

---

📌 **Question 1**  
Within the execution for **Yellow Taxi** data for the year **2020** and month **12**, what is the uncompressed file size of  
`yellow_tripdata_2020-12.csv`?

✅ **Answer:** **128.3 MiB**

---

📌 **Question 2**  
What is the rendered value of the variable `file` when:
- taxi = **green**
- year = **2020**
- month = **04**

Template used:  
`{{inputs.taxi}}_tripdata_{{inputs.year}}-{{inputs.month}}.csv`

✅ **Answer:**  
`green_tripdata_2020-04.csv`

---

📌 **Question 3**  
How many rows exist for **Yellow Taxi** data across **all CSV files** in **2020**?

✅ **Answer:** **24,648,499 rows**

🔍 **Query used:**
```sql
SELECT
  COUNT(*)
FROM
  `nth-weft-417606.ny_taxi_dataset.yellow_tripdata`
WHERE
  tpep_pickup_datetime >= TIMESTAMP('2020-01-01 00:00:00')
  AND tpep_pickup_datetime < TIMESTAMP('2021-01-01 00:00:00');
```
---
📌 **Question 4**  
How many rows exist for Green Taxi data across **all CSV files** in **2020**?

✅ **Answer**: **1,734,051 rows**

🔍 **Query used:**  
```sql
SELECT
  COUNT(*)
FROM
  `nth-weft-417606.ny_taxi_dataset.green_tripdata`
WHERE
  lpep_pickup_datetime >= TIMESTAMP('2020-01-01 00:00:00')
  AND lpep_pickup_datetime < TIMESTAMP('2021-01-01 00:00:00');
```
📌 **Question 5**  
How many rows are in the Yellow Taxi dataset for **March 2021**?

✅ **Answer**: **1,925,152** rows

🔍 **Query used:**
```sql
SELECT
  COUNT(*)
FROM
  `nth-weft-417606.ny_taxi_dataset.yellow_tripdata`
WHERE
  tpep_pickup_datetime >= TIMESTAMP('2021-03-01 00:00:00')
  AND tpep_pickup_datetime < TIMESTAMP('2021-04-01 00:00:00');
```

📌 **Question 6**  
How do you configure the timezone to New York in a Kestra Schedule trigger?

✅ **Answer**: Add a timezone property set to America/New_York in the Schedule trigger configuration

