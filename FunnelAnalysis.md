# Funnel Analysis System Design

## Architecture
```
front-end           backend
XMLHttpsRequest ->  ApacheLog -> Flume -> Kafka -> Spark -> Hive      




```




## DB and schema
- original table

| account_id    | date          | event_id  | channel   | device   |
| ------------- | :-----------: | :-------: | :-------: |:-------: |
| 000033        | 2021-03-13    |      1    |    ??     |     ??
| 000063        | 2021-03-13    |      4    |
| 000050        | 2021-03-13    |      3    |
| 000001        | 2021-03-12    |      1    |
| 000053        | 2021-03-12    |      1    |
| ......        | ..........    |     ...   |


## Funnel analysis (by given time range, by channel, by device)

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

| event_id | event_count   | event_percentage  | drop_off_rate_by_step  |
| -------- | :-----------: | :---------------: | :--------------------: |
| 1        | 150           |  1                |  NULL
| 2        | 80            |  0.5333           |  0,4667
| 3        | 80            |  0.5333           |  0
| 4        | 40            |  0.2667           |  0.5
| 5        | 10            |  0.0667           |  0,75
| 6        | 7             |  0.0467           |  0.3



## High availability and efficiency
  





