FROM ubuntu:20.04

RUN apt-get update
RUN apt-get install -y wget
RUN apt-get install -y python3-pip

RUN pip install pandas sqlalchemy psycopg2-binary

WORKDIR /app
COPY ingest_data.py ingest_data.py 
COPY ingest_data_2.py ingest_data_2.py
