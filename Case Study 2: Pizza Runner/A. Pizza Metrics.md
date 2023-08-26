# 🍕CASE STUDY 2: PIZZA RUNNER 

## 💥 Solution - A.Pizza Metrics
***
### 1. How many pizzas were ordered?
``` sql
SELECT
COUNT(*) AS total_pizza_ordered
FROM pizza_runner.customer_orders
```
#### Answer:
![image](https://user-images.githubusercontent.com/108972584/262908469-23e7d6af-4389-49e8-844b-a47e8b69a269.png)
### 2. How many unique customer orders were made?
```sql
SELECT
COUNT(DISTINCT order_id) AS unique_orders_count
FROM pizza_runner.customer_orders
```
#### Answer:
![image](https://user-images.githubusercontent.com/108972584/262911147-510fedb8-7b2b-4121-9a71-bd351664160c.png)
### 3. How many successful orders were delivered by each runner?
```sql
SELECT
runner_id
,COUNT(order_id) successful_orders_count
FROM pizza_runner.runner_orders
WHERE cancellation IS NULL
GROUP BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263240530-df453bd2-966e-406b-b65f-9128ee512f05.png)
### 4. How many of each type of pizza was delivered?
```sql
SELECT
p.pizza_name
,COUNT(c.pizza_id) pizza_count
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
	ON c.order_id = r.order_id
LEFT JOIN pizza_runner.pizza_names p
	ON c.pizza_id = p.pizza_id
WHERE r.cancellation IS NULL
GROUP BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263243851-034b0f13-0b48-4a92-8534-7fae91f896b8.png)
### 5. How many Vegetarian and Meatlovers were ordered by each customer?
```sql
SELECT
customer_id
,SUM(CASE WHEN pizza_id=1 THEN 1 ELSE 0 END ) Meatlovers
,SUM(CASE WHEN pizza_id=2 THEN 1 ELSE 0 END ) Vegetarian
FROM pizza_runner.customer_orders
GROUP BY 1
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263247634-126ea57b-0538-48df-bd42-5f1dc6cdc953.png)
### 6. What was the maximum number of pizzas delivered in a single order?
```sql
SELECT
MAX(pizza_count) max_pizza
FROM( 
SELECT
c.order_id
,COUNT(*) pizza_count
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
	ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY 1 ) count_pizza
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263250304-06cf9734-9e05-4eed-9e19-370572ec4d8a.png)
### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
SELECT
customer_id
,SUM(CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1 ELSE 0 END) at_least_1_change
,SUM(CASE WHEN exclusions IS NULL AND extras IS NULL THEN 1 ELSE 0 END) no_change
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
	ON 	r.order_id = c.order_id
WHERE cancellation IS NULL
GROUP BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263253587-0b6cb41c-eb90-49b6-b550-8c56089e7d44.png)
### 8. How many pizzas were delivered that had both exclusions and extras?
```sql
SELECT
COUNT(c.order_id) pizza_count
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
	ON 	r.order_id = c.order_id
WHERE cancellation IS NULL AND exclusions IS NOT NULL AND extras IS NOT NULL
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263449166-74324ed6-e9b0-4ce7-8c3e-3c7757013e93.png)
### 9. What was the total volume of pizzas ordered for each hour of the day?
```SQL
SELECT
DATE_PART('HOUR',order_time) hour_of_day
,COUNT(order_id) pizza_order
FROM pizza_runner.customer_orders
GROUP BY 1
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263452275-72f4a45f-dd6b-41b7-a1d4-4aab929f2975.png)
### 10. What was the volume of orders for each day of the week?
```sql
SELECT
  TO_CHAR(order_time, 'Day') AS day_of_week,
  COUNT(order_id) AS pizza_count
FROM pizza_runner.customer_orders
GROUP BY day_of_week, DATE_PART('dow', order_time)
ORDER BY DATE_PART('dow', order_time);
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/263453429-17ced325-242c-414a-8d61-10abcf885f97.png)
