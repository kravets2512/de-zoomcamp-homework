### Data ingestion

Build the image with scripts

```bash
docker build -t taxi_ingest:v001 .
```
Run docker-compose for postgres and pgadmin

```bash
docker-compose up -d
```
Run the image with scripts

```bash
docker run -it --network=week_1_docker_sql_default taxi_ingest:v001
```

Download yellow_tripdata_2021-01

```bash
URL_1="https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv"

python3 ingest_data.py --user=root --password=root --host=pgdatabase --port=5432 --db=ny_taxi --table_name=yellow_taxi_trips --url=${URL_1}
```

Download taxi+_zone_lookup

```bash
URL_2="https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv"

python3 ingest_data_2.py --user=root --password=root --host=pgdatabase --port=5432 --db=ny_taxi --table_name=taxi_zone --url=${URL_2}
```
### SQL 

How many taxi trips were there on January 15?

```sql
SELECT DATE(tpep_pickup_datetime) as dt,
       count(*) as cnt
FROM yellow_taxi_trips
GROUP by dt
HAVING DATE(tpep_pickup_datetime) = '2021-01-15';
```

On which day it was the largest tip in January? (note: it's not a typo, it's "tip", not "trip")

```sql
SELECT DATE(tpep_pickup_datetime) as dt,
       max(tip_amount) as tip
FROM yellow_taxi_trips
GROUP by dt
ORDER BY tip desc
LIMIT 1;
```

Most popular destination

```sql
SELECT t1."DOLocationID",
       t3."Zone",
	   count(*) as cnt
FROM yellow_taxi_trips as t1
JOIN taxi_zone as t2 on t2."LocationID" = t1."PULocationID"
JOIN taxi_zone as t3 on t3."LocationID" = t1."DOLocationID"
WHERE DATE(t1.tpep_pickup_datetime) = '2021-01-14'
AND t2."Zone" = 'Central Park'
GROUP BY t1."DOLocationID",
       t3."Zone"
ORDER BY cnt desc
LIMIT 1;
```

Most expensive route

```sql
SELECT CONCAT(CASE WHEN t2."Zone" IS NULL THEN 'Unknown' ELSE t2."Zone" END,
			  '/', 
			  CASE WHEN t3."Zone" IS NULL THEN 'Unknown' ELSE t3."Zone" END) as pair_pu_do,
	   avg(t1.total_amount) as avg_total_amount
FROM yellow_taxi_trips as t1
JOIN taxi_zone as t2 on t2."LocationID" = t1."PULocationID"
JOIN taxi_zone as t3 on t3."LocationID" = t1."DOLocationID"
GROUP BY pair_pu_do
ORDER BY avg_total_amount desc
LIMIT 1
;
```