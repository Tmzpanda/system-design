# Funnel Analysis System Design

## Architecture

```
front-end           backend
XMLHttpRequest ->   Apache2 Log -> Flume -> Kafka -> Spark -> Hive      

```


## DB and schema
- Suppose we have the below table (with schema and sample data) in Hive:

  | account_id    | date          | event_id  | channel   | device   |
  | ------------- | :-----------: | :-------: | :-------: |:-------: |
  | 000033        | 2021-03-13    |      1    |    ??     |     ??
  | 000063        | 2021-03-13    |      4    |
  | 000050        | 2021-03-13    |      3    |
  | 000001        | 2021-03-12    |      1    |
  | 000053        | 2021-03-12    |      1    |
  | ......        | ..........    |     ...   |


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

- To further achive funnel analysis based on given time range, channel, or device, we can simply add constraints on the above query logic.



## High availability and efficiency

- To achieve HA (high availability), we need to consider the below conpotents:
  - **Backend servers**: use load balancer and multiple servers to handle the traffic load and failover
  - **Flume agents** and **Kafka brokers**: use ZooKeeper to achieve HA for these components
  - **Spark cluster**: configuration of Yarn and Spark's master-slave architecture will ensure the HA.
  - **Hive**: the underlying HDFS supports replication

- In terms of efficiency, cache.





