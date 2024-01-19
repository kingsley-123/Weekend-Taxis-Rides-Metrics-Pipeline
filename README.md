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
 WITH SaturdayTrip AS (
            -- Subquery to get relevant data for Saturdays
            SELECT
                DATE_FORMAT(pickup_date, '%Y-%m') AS month,
                COUNT(pickup_date) AS total_number_of_trips_per_saturday,
                COUNT(*) AS total_trips,
                SUM(fare_amount) AS total_fare_amount,
                AVG(TIMESTAMPDIFF(MINUTE, pickup_datetime, dropoff_datetime)) AS avg_duration
            FROM
                tripdata
            WHERE
                pickup_date >= '2014-01-01' AND pickup_date <= '2016-12-31'
                AND DAYOFWEEK(pickup_date) = 6 -- 6 corresponds to Saturday
            GROUP BY
                pickup_date
        ),
        SundayTrip AS (
            -- Subquery to get relevant data for Sundays
            SELECT
                DATE_FORMAT(pickup_date, '%Y-%m') AS month,
                COUNT(pickup_date) AS total_number_of_trips_per_sunday,
                COUNT(*) AS total_trips,
                SUM(fare_amount) AS total_fare_amount,
                AVG(TIMESTAMPDIFF(MINUTE, pickup_datetime, dropoff_datetime)) AS avg_duration
            FROM
                tripdata
            WHERE
                pickup_date >= '2014-01-01' AND pickup_date <= '2016-12-31'
                AND DAYOFWEEK(pickup_date) = 7 -- 1 corresponds to Sunday
            GROUP BY
                pickup_date
        )
        SELECT
            st.month as month,
            ROUND(AVG(st.total_number_of_trips_per_saturday), 2) AS sat_mean_trip_count,
            ROUND(SUM(st.total_fare_amount) / SUM(st.total_trips), 2) AS sat_mean_fare_per_trip,
            ROUND(AVG(st.avg_duration), 2) AS sat_mean_duration_per_trip,
            ROUND(AVG(su.total_number_of_trips_per_sunday), 2) AS sun_mean_trip_count,
            ROUND(SUM(su.total_fare_amount) / SUM(su.total_trips), 2) AS sun_mean_fare_per_trip,
            ROUND(AVG(su.avg_duration), 2) AS sun_mean_duration_per_trip
        FROM
            SaturdayTrip st
         JOIN
            SundayTrip su ON st.month = su.month 
        GROUP BY
            month
        ORDER BY
            month
