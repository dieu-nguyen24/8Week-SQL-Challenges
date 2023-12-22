## The Problem
The full information about the case study can be found at: [Link](https://8weeksqlchallenge.com/case-study-2/)



***
## Entity Relationship Diagram

***
## Data Cleaning

### Removing NULL values and ensuring consistency
```sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT order_id, 
		customer_id, 
        pizza_id, 
        CASE 
        	WHEN exclusions LIKE 'null' THEN '' 
        	ELSE exclusions 
        END AS exclusions,
        CASE 
        	WHEN extras LIKE 'null' OR extras IS NULL THEN '' 
        	ELSE extras
        END AS extras,
        order_time
FROM pizza_runner.customer_orders;

SELECT * FROM customer_orders_temp;
```

| order_id | customer_id | pizza_id | exclusions | extras | order_time               |
| -------- | ----------- | -------- | ---------- | ------ | ------------------------ |
| 1        | 101         | 1        |            |        | 2020-01-01T18:05:02.000Z |
| 2        | 101         | 1        |            |        | 2020-01-01T19:00:52.000Z |
| 3        | 102         | 1        |            |        | 2020-01-02T23:51:23.000Z |
| 3        | 102         | 2        |            |        | 2020-01-02T23:51:23.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 1        | 4          |        | 2020-01-04T13:23:46.000Z |
| 4        | 103         | 2        | 4          |        | 2020-01-04T13:23:46.000Z |
| 5        | 104         | 1        |            | 1      | 2020-01-08T21:00:29.000Z |
| 6        | 101         | 2        |            |        | 2020-01-08T21:03:13.000Z |
| 7        | 105         | 2        |            | 1      | 2020-01-08T21:20:29.000Z |
| 8        | 102         | 1        |            |        | 2020-01-09T23:54:33.000Z |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10T11:22:59.000Z |
| 10       | 104         | 1        |            |        | 2020-01-11T18:34:49.000Z |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11T18:34:49.000Z |

```sql
CREATE TEMP TABLE runner_orders_temp AS
SELECT order_id, 
		runner_id, 
        CASE 
        	WHEN pickup_time LIKE 'null' THEN '' 
        	ELSE pickup_time
        END AS pickup_time,
        CASE
		WHEN distance LIKE '%km%' THEN REPLACE(distance, 'km', '')
		WHEN distance LIKE 'null' THEN ''
		ELSE distance
         END AS distance,
         CASE
        	WHEN duration LIKE '%min%' THEN REGEXP_REPLACE(duration, '[^\d]', '', 'g')
            	WHEN duration LIKE 'null' THEN ''
            	ELSE duration
         END AS duration,
         CASE 
         	WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ''
         	ELSE cancellation
         END AS cancellation
FROM pizza_runner.runner_orders;

SELECT * FROM runner_orders_temp;
```


