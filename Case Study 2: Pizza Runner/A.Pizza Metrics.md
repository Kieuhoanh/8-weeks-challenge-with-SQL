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
