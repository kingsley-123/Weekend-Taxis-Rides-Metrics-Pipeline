# Weekend-Taxis-Rides-Metrics-Pipeline

## Purpose
The objective of this project is to fetch and analyze monthly metrics related to weekend taxi rides in New York City between 2014 and 2016. 
The metrics include the average number of trips, average fare per trip, and average duration per trip for both Saturdays and Sundays. 
The data is sourced from the New York City taxi trip dataset stored in a Clickhouse database.

## Architecture
![Architecture](https://github.com/kingsley-123/Weekend-Taxis-Rides-Metrics-Pipeline/assets/63650573/d36ff5d8-ded0-4f3f-b3c7-c1ac6e002a64)

## Data Flow
1. **Data Extraction:** SQL queries are used to extract relevant data from the Clickhouse database, focusing on the New York City taxi trip dataset for the specified timeframe.
2. **Data Transformation:** The extracted data is transformed to calculate the required monthly metrics, including the average number of trips, fare per trip, and duration per trip for both Saturdays and Sundays.
3. **Data Loading:** The transformed metrics are loaded into a SQLite database, providing portability and ease of use.

## Technologies Used
- **Clickhouse Database:** Source of raw taxi trip data.
- **SQLite Database:** Destination for storing transformed metrics.
- **Apache Airflow:** Orchestrates the ETL pipeline, scheduling and executing tasks.
- **SQL:** Used for querying data from Clickhouse and defining data manipulations.
- **DAG (Directed Acyclic Graph):** Represents the workflow of the ETL pipeline.

## Data Model
The data model includes a table in the SQLite database with columns representing the monthly metrics for both Saturdays and Sundays.
![Data Model](https://github.com/kingsley-123/Weekend-Taxis-Rides-Metrics-Pipeline/assets/63650573/49bf1fd7-d4b5-4658-a041-beb97502b42b)

## SQL Query
SQL Query for fetching weekend taxi ride metrics
![SQL DB and Table](https://github.com/kingsley-123/Weekend-Taxis-Rides-Metrics-Pipeline/assets/63650573/5482b6a4-a529-4779-9805-c9e3b4e95ab7)
