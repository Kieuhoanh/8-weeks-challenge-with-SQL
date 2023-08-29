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
,DATE_PART('minute',AGE(pickup_time::TIMESTAMP,order_time))::INTEGER AS time_pickup
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r 
 	ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY 1,3
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263991546-36b7b7c3-cfb2-4b09-b3dc-2e040f0455ea.png)
### 4.What was the average distance travelled for each customer?
```sql
SELECT
c.customer_id
,TO_CHAR(AVG(r.distance),'FM999.00')
FROM pizza_runner.runner_orders r 
LEFT JOIN pizza_runner.customer_orders c  
	ON c.order_id=r.order_id
WHERE cancellation IS NULL
GROUP BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263998259-54b0ecc4-5145-4aa8-bcf8-900cb94b28cb.png)
### 5.What was the difference between the longest and shortest delivery times for all orders?
```sql
SELECT
MAX(duration)-MIN(duration) max_diff
FROM pizza_runner.runner_orders 
WHERE cancellation IS NULL
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/264001153-af2523a7-fb29-45c3-a5b8-de6ed22edbb2.png)
### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
