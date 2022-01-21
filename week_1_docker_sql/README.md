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