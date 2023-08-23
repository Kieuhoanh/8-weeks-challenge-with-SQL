# 🍕CASE STUDY 2: PIZZA RUNNER

## 🧰 CLEAN DATA
### 🛠 Table: Customer_orders:
Looking at the customer_orders table below, we can see that there are:
- In the exclusions column, there are missing/ blank spaces ' ' and null values.
- In the extras column, there are missing/ blank spaces ' ' and null values.
![image](https://user-images.githubusercontent.com/81607668/129472388-86e60221-7107-4751-983f-4ab9d9ce75f0.png)
Our course of action to clean the table:
- Create a temporary table with all the columns
- Remove null values in exlusions and extras columns and replace with blank space ' '.
``` sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT
order_id
,customer_id
,pizza_id
, CASE 
	WHEN exclusions IS null OR exclusions LIKE 'null' THEN ''
	ELSE exclusions
	END exclusions
, CASE 
	WHEN extras IS NULL OR extras LIKE 'null' THEN ''
	ELSE extras
	END extras
,order_time
FROM pizza_runner.customer_orders
```
