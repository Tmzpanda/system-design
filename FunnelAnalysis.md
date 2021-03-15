# Funnel Analysis System Design

## Architecture
- Below is the sketch of the architecture to collect and organize the original data:
```
front-end           backend
XMLHttpRequest ->   Apache2 Log -> Flume -> Kafka -> Spark -> Hive      

```
It can be descirbed as follow steps:
  - 1. front-end code track and send user bahavior variable via **XMLHttpRequest** to backend server **Apache2 Log** 
    (components may be variant depending on the teck stack being used).
  - 2. **Flume** will collect user behavior log and produce log data to messaging system **Kafka**.
  - 3. The log data can be further validated, reformatted per bussiness requirement through distributed processing framework **Spark**.
  - 4. The organized and structured data will be loaded to data warehouse **Hive** tables, on top of which we can achive and scale funnel analysis.

 

## DB and schema
- Suppose we have the below events table (with schema and sample data) in Hive (fact table enriched with dimension tables):

  | account_id    | datetime               | event_id  | channel     | device     |
  | ------------- | :--------------------: | :-------: | :---------: |:---------: |
  | 000033        | 2021-03-14 22:01:45    |      1    |   search    |   mobile   |
  | 000063        | 2021-03-14 22:02:36    |      4    |   paid ads  |   browser  |
  | 000033        | 2021-03-14 22:02:05    |      2    |   search    |   mobile   |
  | 000001        | 2021-03-14 22:03:12    |      1    |   paid ads  |   browser  |
  | 000053        | 2021-03-14 22:04:43    |      1    |   paid ads  |   browser  |
  | ......        | ..............         |     ...   |   ...       |    ...     |



## Funnel analysis (by given time range, by channel, by device)

- To view the key metrics (e.g. conversion rate, drop off rate at each step) of the above table, we can implement the below logic:
  ```SQL
  WITH 
  grouped_events AS(
    SELECT event_id, COUNT(*) AS event_count
    FROM events
    GROUP BY event_id ORDER BY COUNT(*) DESC
  )

  SELECT event_id, event_count, 
  ROUND(event_count/(SELECT MAX(event_count) FROM grouped_events), 4) AS event_percentage,
  1 - ROUND(event_count/(LAG(event_count, 1) OVER (ORDER BY event_count DESC)), 4) AS drop_off_rate_by_step
  FROM grouped_events
  ORDER BY event_count DESC

  ```
  Consequently, we can get the below sample output:

  | event_id | event_count   | event_percentage  | drop_off_rate_by_step  |
  | -------- | :-----------: | :---------------: | :--------------------: |
  | 1        | 150           |  1                |  NULL
  | 2        | 80            |  0.5333           |  0,4667
  | 3        | 80            |  0.5333           |  0
  | 4        | 40            |  0.2667           |  0.5
  | 5        | 10            |  0.0667           |  0,75
  | 6        | 7             |  0.0467           |  0.3

- To further achive multi-channel funnel analysis based on given time range, channel, or device, we can simply add constraints on the above query logic. 
  For example, to achive funnel analysis on mobile device, we can add a filter ``` WHERE device = 'mobile'``` to the above query as below:
  ```SQL
  WITH 
  grouped_events AS(
    SELECT event_id, COUNT(*) AS event_count
    FROM events
    GROUP BY event_id ORDER BY COUNT(*) DESC
    WHERE device = 'mobile'                   -- filter 
  )

  SELECT event_id, event_count, 
  ROUND(event_count/(SELECT MAX(event_count) FROM grouped_events), 4) AS event_percentage,
  1 - ROUND(event_count/(LAG(event_count, 1) OVER (ORDER BY event_count DESC)), 4) AS drop_off_rate_by_step
  FROM grouped_events
  ORDER BY event_count DESC

  ```

## High availability and efficiency
If we deploy the system in production environment:
- To achieve HA (high availability), we need to consider the below conpotents:
  - **Backend servers**: use load balancer and multiple servers to handle the traffic load and failover
  - **Flume agents** and **Kafka brokers**: use ZooKeeper to achieve HA for these components
  - **Spark cluster**: configuration of Yarn and Spark's master-slave architecture will ensure the HA.
  - **Hive**: the underlying HDFS supports replication

- In terms of efficiency
  - add **cache** layer which stores recent query data to reduce response time.
  - leverage hive **partition and bucket** table to avoid full table scan and search only on the target partition/bucket.






