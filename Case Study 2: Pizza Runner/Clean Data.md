# 🍕CASE STUDY 2: PIZZA RUNNER

## 🧰 CLEAN DATA
### 🛠 Table: Customer_orders
Looking at the customer_orders table below, we can see that there are:
- In the exclusions column, there are missing/ blank spaces ' ' and null values.
- In the extras column, there are missing/ blank spaces ' ' and null values.
![image](https://user-images.githubusercontent.com/81607668/129472388-86e60221-7107-4751-983f-4ab9d9ce75f0.png)
#### Our course of action to clean the table:
##### Method 1
- Create a temporary table with all the columns
- Remove null values in exlusions and extras columns and replace with blank space ' '.
``` sql
CREATE TEMP TABLE customer_orders_temp AS
SELECT
order_id
,customer_id
,pizza_id
, CASE 
	WHEN exclusions ='' OR exclusions LIKE 'null' THEN NULL
	ELSE exclusions
	END exclusions
, CASE 
	WHEN extras ='' OR extras LIKE 'null' THEN NULL
	ELSE extras
	END extras
,order_time
FROM pizza_runner.customer_orders
```
This is customers_orders_temp table and we will use this table to run all our queries.
![image](https://user-images.githubusercontent.com/81607668/129472551-fe3d90a0-1e8b-4f32-a2a7-2ecd3ac469ef.png)
##### Method 2
- Update data directly of exclusions column and extras column in customer_orders table
```sql
UPDATE pizza_runner.customer_orders
SET exclusions = NULL
WHERE exclusions ='' OR exclusions LIKE 'null';
UPDATE pizza_runner.customer_orders
SET extras = NULL
WHERE extras ='' OR extras LIKE 'null';
```
![image](https://user-images.githubusercontent.com/108972584/263035028-24f23d39-6fdf-47a5-963a-bb94541fe4c2.png)

### 🛠 Table: runner_orders
Looking at the runner_orders table below, we can see that there are:
- In the pickup_time column, there are missing/ blank spaces ' ' and null values.
- In the distance column, there are missing/ blank spaces ' ' and null values.
- In the duration column, there are missing/ blank spaces ' ' and null values.
- In the cancellation column, there are missing/ blank spaces ' ' and null values.
![image](https://user-images.githubusercontent.com/81607668/129472585-badae450-52d2-442e-9d50-e4d0d8fce83a.png)
#### Our course of action to clean the table:
##### Method 1
- In pickup_time column, remove nulls and replace with blank space ' '.
- In distance column, remove "km" and nulls and replace with blank space ' '.
- In duration column, remove "minutes", "minute" and nulls and replace with blank space ' '.
- In cancellation column, remove NULL and null and and replace with blank space ' '.
``` sql
CREATE TEMP TABLE runner_orders_temp AS
SELECT
order_id
,runner_id
, CASE 
	WHEN pickup_time LIKE 'null' THEN ''
	ELSE pickup_time
	END pickup_time
, CASE 
	WHEN distance LIKE 'null' THEN ''
	WHEN distance LIKE '%km' THEN TRIM('km' FROM distance)
	ELSE distance
	END distance
,CASE 
	WHEN duration LIKE 'null' THEN ''
	WHEN duration LIKE '%mins' THEN TRIM('mins' FROM distance)
	WHEN duration LIKE '%minute' THEN TRIM('minute' FROM distance) 
	WHEN duration LIKE '%minutes' THEN TRIM('minminutes' FROM distance)
	ELSE duration
	END duration
, CASE 
	WHEN cancellation LIKE 'null' OR cancellation IS NULL THEN ''
	ELSE cancellation
	END cancellation
FROM pizza_runner.runner_orders
```
This is runner_orders_temp table and we will use this table to run all our queries.
![image](https://user-images.githubusercontent.com/81607668/129472778-6403381d-6e30-4884-a011-737b1eff7379.png)
#### Method 2
- Update data directly in runner_orders table
```sql
UPDATE pizza_runner.runner_orders
SET pickup_time = NULL
WHERE pickup_time ='' OR pickup_time LIKE 'null';

UPDATE pizza_runner.runner_orders
SET distance = NULL
WHERE distance ='' OR distance LIKE 'null';

UPDATE pizza_runner.runner_orders
SET distance = TRIM('km' FROM distance)
WHERE distance LIKE '%km';

UPDATE pizza_runner.runner_orders
SET duration = NULL
WHERE duration ='' OR duration LIKE 'null';

UPDATE pizza_runner.runner_orders
SET duration = LEFT(duration,2)
WHERE duration LIKE '%mins' OR duration LIKE '%minute' OR duration LIKE '%minutes';

UPDATE pizza_runner.runner_orders
SET cancellation = NULL
WHERE cancellation ='' OR cancellation LIKE 'null';
```
![image](https://user-images.githubusercontent.com/108972584/265570113-c9614acc-341a-43e6-8643-c118218e2165.png)
