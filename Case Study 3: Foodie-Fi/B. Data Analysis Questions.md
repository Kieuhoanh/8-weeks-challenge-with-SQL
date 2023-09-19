# 🥗 CASE STUDY 3: FOODIE-FI
## 🔑 Solution - B. Data Analysis Questions
***
### 1. How many customers has Foodie-Fi ever had?
```sql
SELECT
COUNT(DISTINCT customer_id) AS total_customers
FROM foodie_fi.subscriptions
```
#### Answer:
![image](https://user-images.githubusercontent.com/108972584/267304755-7c1a6a0c-327d-4cd0-83cc-cfe52de305f7.png)
### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
```SQL
SELECT
DATE_TRUNC('month',start_date):: DATE month_start
,COUNT(*) trial_customers
FROM foodie_fi.subscriptions
WHERE plan_id = 0
GROUP BY 1
ORDER BY 1
```
#### Answer:
![image](https://user-images.githubusercontent.com/108972584/267306524-df7195b8-06ea-4092-b63c-538f57642f7a.png)
### 3.What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
```sql
SELECT
p.plan_id
,plan_name
,COUNT(*)
FROM foodie_fi.subscriptions s
LEFT JOIN foodie_fi.plans p
ON p.plan_id = s.plan_id
WHERE start_date > '2020-12-31'
GROUP BY 1,2
ORDER BY 1
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268878524-e21e617c-b5a9-47e4-ac36-3aedb5895638.png)
### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```SQL
SELECT
SUM(CASE WHEN plan_id = 4 THEN 1 ELSE 0 END) churned_cus
,ROUND(100.0*SUM(CASE WHEN plan_id = 4 THEN 1 ELSE 0 END)/COUNT(DISTINCT customer_id),1) percentage
FROM foodie_fi.subscriptions
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268881356-cd411c21-81e1-4d52-95b2-4ed6981f352e.png)
### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
```SQL
WITH cte_rank AS ( 
SELECT
customer_id
,plan_id
,ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY start_date) rank
FROM foodie_fi.subscriptions
)
SELECT
SUM(CASE WHEN plan_id = 4 THEN 1 ELSE 0 END) churned_straight
,ROUND(100.0*SUM(CASE WHEN plan_id = 4 THEN 1 ELSE 0 END)/COUNT(*),1) percentage
FROM cte_rank
WHERE rank = 2
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268886200-b61a3eb6-012a-48c6-9f0c-edc84fe017c5.png)
### 6. What is the number and percentage of customer plans after their initial free trial?
```sql
WITH cte_rank AS ( 
SELECT
customer_id
,plan_id
,ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY start_date) rank
FROM foodie_fi.subscriptions
WHERE plan_id != 0
)
SELECT
p.plan_id
,p.plan_name
,COUNT(*) customer_number
,ROUND(100*COUNT(*)/SUM(COUNT(*))OVER()) percentage
FROM cte_rank r
LEFT JOIN foodie_fi.plans p
ON p.plan_id = r.plan_id
WHERE rank = 1
GROUP BY 1,2
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268903099-6bfde434-d3a9-42f3-8313-da51ef067c95.png)
### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
```sql
WITH cte_ranked AS( 
SELECT
customer_id
,plan_id
,start_date
,ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY start_date DESC) rank
FROM foodie_fi.subscriptions
WHERE start_date <= '2020-12-31'
)
SELECT
r.plan_id
,p.plan_name
,COUNT(*) customers
,ROUND(100*COUNT(*)/SUM(COUNT(*)) OVER(),1) percentage
FROM cte_ranked r
LEFT JOIN foodie_fi.plans p
ON p.plan_id = r.plan_id
WHERE rank = 1
GROUP BY 1,2
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268905755-e89a8f5e-ab59-4365-ac9d-f3f0790e8005.png)
### 8.How many customers have upgraded to an annual plan in 2020?
```sql
SELECT
COUNT(*) annual_customers
FROM foodie_fi.subscriptions
WHERE start_date <= '2020-12-31' AND start_date >= '2020-01-01' AND plan_id = 3
```
#### Answer
![image](https://user-images.githubusercontent.com/108972584/268910729-c166a58c-b932-4b9f-ac80-f2da0719a69d.png)
### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
