# Bikeshare Availability

The goal of this project is for bikeshare customer to have near real time information on the availability status of bikeshare docks.

This ETL uses Kafka, Spark Streaming and Postgres. The data consists of records of when bikes leave and enter docks which are in csv files stored in an S3 bucket.


## Table of Contents
1. [Repo Directory Structure](README.md#Repo-Directory-Structure)
2. [Pipeline](README.md#Pipeline)
3. [Approach](README.md#Approach)
4. [Evironment SetUp](README.md#Evironment-Setup)
5. [Demo](README.md#Demo)
6. [Further Extention](README.md#Further-Extention)




## Repo Directory Structure

    ├── README.md
    ├── flask
    │   └── app.py
    │   └── database.py
    │   ├── templates
    │         └── index.html
    ├── kaka
    │   └── producer.py
    │   └── spawn_kafka_streams.sh
    │  
    └── streaming
        └── stream.py
    
## Pipeline

![alt text](pic/pipeline.png)

## Environment Setup
python 3.5
### Cluster Setup
8 AWS EC2 instances:

- (3 nodes) Kafka Cluster
- (3 nodes) Spark Streaming
- Postgres Node
- Flask Node

### Kafka Setup
pip install `boto3` and `kafka-python`

Create Kafka topics using this command

`kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor <rep-factor> --partitions <num-partitions> --topic <topic-name>`

Check that Kafka topic and partitions are as expected using this command 

`kafka-topics.sh --describe --zookeeper localhost:2181 --topic <topic-name>`

Used this command lne to test if Kafka consumer was receiving the messages from the Kafka producer before connecting Spark Streams to Kafka producer

`kafka-console-consumer.sh --zookeeper localhost:2181 --topic <topic-name>`

run the bash script `startProducer.sh` to kick off kafka producer

### Spark Streaming Setup
Add JDBC driver to spark class path

`bin/spark-shell --driver-class-path postgresql-9.4.1207.jar --jars postgresql-9.4.1207.jar`

### PostgreSQL Setup
Change listening address in `postgresql.conf` from local host to the IP addresses that you would like it to listen to

Changed the hosts postgresql is allowed to connect to in `pg_hba.conf` by adding this line to the file

` host    <database>      <user>       0.0.0.0/0        md5`

### Flask Setup
pip install `psycopg2` to access postgres database

pip install `folium` for map in the html

pip install `geopy.geocoders` to convert addresses in number street name form to latitude and longditude

## Demo
[Demo](https://www.youtube.com/watch?v=QS-lSPjHsqQ)

When the customer opens the app they will see blue circles on the New York City map that represent the open docks at that time. They also have the option of searching a location, which will display as a red circle on the map, and blue circles, that represent open docks, in a 3 block radius of the input location would show up on the map. Also there is a flash message if the format of the location is not in the expected format. 

## Further Extention
Further extention of this project could be choas testing
structured spark stream
preprocessing with spark before going into kafka
