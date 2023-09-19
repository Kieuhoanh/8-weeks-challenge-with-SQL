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
![image](

