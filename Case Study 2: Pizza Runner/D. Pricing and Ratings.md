# 🍕CASE STUDY 2: PIZZA RUNNER
## Solution - D. Pricing and Ratings 
***
### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees?
```sql
SELECT
SUM(CASE WHEN pizza_id = 1 THEN 12
		WHEN pizza_id = 2 THEN 10
		END) AS revenue
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265392467-2b9e9a20-1dcb-4f0f-a88a-1f5300180514.png)
### 2. What if there was an additional $1 charge for any pizza extras?
- Add cheese is $1 extra
```SQL
SELECT
SUM(CASE WHEN pizza_id = 1 THEN 12
		WHEN pizza_id = 2 THEN 10
		END+
	COALESCE(
      CARDINALITY(REGEXP_SPLIT_TO_ARRAY(extras, '[,\s]+')),
      0)  
	)
FROM pizza_runner.customer_orders c
LEFT JOIN pizza_runner.runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265398159-f5f85caf-6313-49d9-a51a-489162951e91.png)
### 3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for ratings for each successful customer order between 1 to 5.
```SQL
DROP TABLE IF EXISTS pizza_runner.ratings;
CREATE TABLE pizza_runner.ratings (
  "order_id" INTEGER,
  "rating" INTEGER
);

INSERT INTO pizza_runner.ratings
SELECT
  order_id,
  FLOOR(1 + 5 * RANDOM()) AS rating
FROM pizza_runner.runner_orders
WHERE pickup_time IS NOT NULL;

SELECT
*
FROM pizza_runner.ratings
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265403359-0d21ab93-9be6-4f49-a3d4-5a36ad3b0e40.png)
### 4. Using your newly generated table - can you join all of the information together to form a table which has the following information for successful deliveries?
- customer_id
- order_id
- runner_id
- rating
- order_time
- pickup_time
- Time between order and pickup
- Delivery duration
- Average speed
- Total number of pizzas
```SQL
SELECT
c.customer_id
,r.order_id
,r.runner_id
,rating
,order_time
,pickup_time
,DATE_PART('MINUTE',AGE(pickup_time,order_time)) pickup_time
,duration
,TO_CHAR(60*distance/duration,'FM9990.00') AS avg_speed
,COUNT(pizza_id) pizza_count
FROM pizza_runner.runner_orders r 
LEFT JOIN pizza_runner.customer_orders c 
ON r.order_id = c.order_id
LEFT JOIN pizza_runner.ratings ratings
ON ratings.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY 1,2,3,4,5,6,7,8,9
ORDER BY 1,2
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265571282-99530040-6cbd-4217-8c7c-3987d5b2d931.png)
