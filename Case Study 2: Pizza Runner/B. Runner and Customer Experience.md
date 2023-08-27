# 🍕 CASE STUDY 2: PIZZA RUNNER
## 🌟 Solution
***
### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
SELECT
  DATE_TRUNC('week', registration_date)::DATE + 4 AS registration_week,
  COUNT(*) AS runners
FROM pizza_runner.runners
GROUP BY 1
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263502143-160c927d-3055-4008-b6b2-1f82bfd41b9d.png)
### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
WITH t AS(
SELECT DISTINCT
r.order_id
,order_time
,pickup_time
,DATE_PART('minute', AGE(pickup_time::TIMESTAMP,order_time))::INTEGER AS pickup_minutes
FROM pizza_runner.runner_orders r 
INNER JOIN pizza_runner.customer_orders c 
	ON c.order_id = r.order_id
WHERE pickup_time IS NOT NULL
)
SELECT 
ROUND(AVG(pickup_minutes),3) avg_pickup_minutes
FROM t
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263502791-d650d3c7-741f-4753-9c91-7c25f19f7681.png)
### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
SELECT
c.order_id
,COUNT(pizza_id) qty_pizza
,DATE_PART('minute',AGE(pickup_time::TIMESTAMP,order_time)):INTEGER 
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r 
 	ON c.order_id = r.order_id
GROUP BY 1,2
```
