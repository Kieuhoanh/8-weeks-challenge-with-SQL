# 🍕CASE STUDY 2: PIZZA RUNNER
## 🌈 Solution - C. Ingredient Optimisation
***
### 1. What are the standard ingredients for each pizza?
```SQL
WITH cte_split_pizza_names AS (
SELECT
  pizza_id
  ,REGEXP_SPLIT_TO_TABLE(toppings, '[,\s]+')::INTEGER AS topping_id
FROM pizza_runner.pizza_recipes
)
SELECT
  pizza_id
  ,STRING_AGG(t2.topping_name::TEXT, ', ') AS standard_ingredients
FROM cte_split_pizza_names AS t1
INNER JOIN pizza_runner.pizza_toppings AS t2
  ON t1.topping_id = t2.topping_id
GROUP BY 1
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/264257719-fdb03c1e-4aac-446d-bfe2-9c78a7c4b1f1.png)
### 2. What was the most commonly added extra?
```SQL
WITH cte_split_topping_extra AS (
SELECT
  pizza_id
  ,REGEXP_SPLIT_TO_TABLE(extras, '[,\s]+')::INTEGER AS topping_id
FROM pizza_runner.customer_orders
)
SELECT
  topping_id
  ,COUNT(*) extras_count
FROM cte_split_topping_extra
GROUP BY 1
ORDER BY 2 DESC
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/264277896-eecc4de0-06fe-4ed7-b63e-ab4dc396a4b0.png)
### 3. What was the most common exclusion?
```sql
WITH cte_split_topping_exclusions AS (
SELECT
  pizza_id
  ,REGEXP_SPLIT_TO_TABLE(exclusions, '[,\s]+')::INTEGER AS topping_id
FROM pizza_runner.customer_orders
)
SELECT
  topping_id
  ,COUNT(*) exclusions_count
FROM cte_split_topping_exclusions
GROUP BY 1
ORDER BY 2 DESC
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/264279316-fde10864-33c7-4175-b19c-d0b801d32ae3.png)
### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
```sql
WITH cte_row_number AS(  
SELECT
*
,ROW_NUMBER() OVER () original_row_number
FROM pizza_runner.customer_orders
)
,cte_split_topping AS (
SELECT
order_id
,customer_id
,pizza_id
,REGEXP_SPLIT_TO_TABLE(exclusions, '[,\s]+')::INTEGER AS topping_id_exclusions
,REGEXP_SPLIT_TO_TABLE(extras, '[,\s]+')::INTEGER AS topping_id_extras
,order_time
,original_row_number
FROM cte_row_number
UNION
SELECT
order_id
,customer_id
,pizza_id
,NULL AS topping_id_exclusions
,NULL AS topping_id_extras
,order_time
,original_row_number
FROM cte_row_number
WHERE exclusions IS NULL AND extras IS NULL
)
,cte_string_agg AS( 
SELECT
base.order_id
,base.customer_id
,base.pizza_id
,names.pizza_name
,base.order_time
,base.original_row_number
,STRING_AGG(exclusions.topping_name,', ') exclusions
,STRING_AGG(extras.topping_name,', ') extras
FROM cte_split_topping base
LEFT JOIN pizza_runner.pizza_names names
	ON base.pizza_id = names.pizza_id 
LEFT JOIN pizza_runner.pizza_toppings exclusions
	ON base.topping_id_exclusions = exclusions.topping_id
LEFT JOIN pizza_runner.pizza_toppings extras
	ON base.topping_id_extras = extras.topping_id
GROUP BY 1,2,3,4,5,6
)
,cte_out_put AS ( 
SELECT
order_id
,customer_id
,pizza_id
,pizza_name
,order_time
,original_row_number
,CASE WHEN exclusions IS NULL THEN '' ELSE ' - Exclude ' || exclusions END AS exclusions
,CASE WHEN extras IS NULL THEN '' ELSE ' - Extra ' || extras END AS extras
FROM cte_string_agg
)
SELECT
order_id
,customer_id
,pizza_id
,order_time
,original_row_number
,pizza_name || exclusions || extras AS order_item
FROM cte_out_put
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265193560-78ec02c0-dfe5-427b-a3ac-e4db4cd221de.png)
### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"
```sql
WITH cte_row_number AS(  
SELECT
*
,ROW_NUMBER() OVER () original_row_number
FROM pizza_runner.customer_orders
)
,cte_split_topping AS (
SELECT
order_id
,customer_id
,pizza_id
,REGEXP_SPLIT_TO_TABLE(exclusions, '[,\s]+')::INTEGER AS topping_id_exclusions
,REGEXP_SPLIT_TO_TABLE(extras, '[,\s]+')::INTEGER AS topping_id_extras
,order_time
,original_row_number
FROM cte_row_number
UNION
SELECT
order_id
,customer_id
,pizza_id
,NULL AS topping_id_exclusions
,NULL AS topping_id_extras
,order_time
,original_row_number
FROM cte_row_number
WHERE exclusions IS NULL AND extras IS NULL
)
,cte_add_column_topping AS ( 
SELECT
order_id
,customer_id
,base.pizza_id
,topping_id_exclusions
,topping_id_extras
,order_time
,original_row_number
,REGEXP_SPLIT_TO_TABLE(toppings, '[,\s]+')::INTEGER AS toppings
FROM cte_split_topping AS base
LEFT JOIN pizza_runner.pizza_recipes AS recipes
ON recipes.pizza_id = base.pizza_id
)
,cte_topping_name AS ( 
SELECT
order_id
,customer_id
,pizza_id
,order_time
,original_row_number
,CASE WHEN topping_id_exclusions = toppings THEN NULL 
	WHEN topping_id_extras = toppings THEN '2x'||t2.topping_name
	ELSE t2.topping_name
	END AS ingredient
FROM cte_add_column_topping t1
LEFT JOIN pizza_runner.pizza_toppings t2
ON t2.topping_id = t1.toppings
)
,cte_out_put AS ( 
SELECT
order_id
,customer_id
,pizza_id
,order_time
,original_row_number
,STRING_AGG(ingredient,', ') ingredient_list
FROM cte_topping_name 
GROUP BY 1,2,3,4,5
)
SELECT
order_id
,customer_id
,t3.pizza_id
,order_time
,original_row_number
,pizza_name|| ': ' || ingredient_list AS ingredient_list
FROM cte_out_put t3
LEFT JOIN pizza_runner.pizza_names t4
ON t3.pizza_id = t4.pizza_id
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/265228116-418889c3-1963-453c-97cb-d9784dabd062.png)
