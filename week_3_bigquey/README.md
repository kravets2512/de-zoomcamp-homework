1. What is count for fhv vehicles data for year 2019

```sql
CREATE OR REPLACE EXTERNAL TABLE `local-dialect-323406.nytaxi.for_hire_vehicles`
OPTIONS (
  format = 'Parquet',
  uris = ['gs://dtc_data_lake_local-dialect-323406/raw/for_hire_vehicles/fhv_tripdata_2019-*.parquet']
);

SELECT COUNT(*) as cnt
 FROM `local-dialect-323406.nytaxi.for_hire_vehicles`;
 ```

 2. How many distinct dispatching_base_num we have in fhv for 2019

 ```sql
 SELECT COUNT(DISTINCT dispatching_base_num) as cnt     
 FROM `local-dialect-323406.nytaxi.for_hire_vehicles`
;
```

4. What is the count, estimated and actual data processed for query which counts trip between 2019/01/01 and 2019/03/31 for dispatching_base_num B00987, B02060, B02279

```sql
CREATE OR REPLACE TABLE `local-dialect-323406.nytaxi.for_hire_vehicles_partitioned_clustered`
PARTITION BY
  DATE(pickup_datetime)
CLUSTER BY dispatching_base_num AS
SELECT * FROM `local-dialect-323406.nytaxi.for_hire_vehicles`;

SELECT COUNT(*) 
FROM `local-dialect-323406.nytaxi.for_hire_vehicles_partitioned_clustered`
WHERE DATE(pickup_datetime) between '2019-01-01' and '2019-03-31'
AND dispatching_base_num in ('B00987', 'B02060', 'B02279');
```
