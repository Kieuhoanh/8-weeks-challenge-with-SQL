# 🏦 CASE STUDY 4: DATA BANK

## Solution - A. Customer Nodes Exploration

### 1. How many unique nodes are there on the Data Bank system?
````SQL
SELECT
COUNT (DISTINCT node_id)
FROM data_bank.customer_nodes
````
#### Answer:
| Count |
| ------|
| 5     |

### 2. What is the number of nodes per region?
```sql
SELECT
n.region_id
,r.region_name
,COUNT (n.node_id) count_nodes
FROM data_bank.customer_nodes n
LEFT JOIN data_bank.regions r ON r.region_id = n.region_id
GROUP BY n.region_id,r.region_name
ORDER BY n.region_id
````
#### Answer:
|region_id|region_name|count_nodes|
|---------|-----------|-----------|
|1        |Australia  |770        |
|2        |America    |735        |
|3        |Africa     |714        |
|4        |Asia       |665        |
|5        |Europe     |616        |

### 3.How many customers are allocated to each region?
````sql
SELECT
n.region_id
,r.region_name
,COUNT(DISTINCT customer_id) count_customer
FROM data_bank.customer_nodes n
LEFT JOIN data_bank.regions r ON n.region_id = r.region_id
GROUP BY n.region_id,r.region_name
````
#### Answer:
|region_id|region_name|count_customer|
|---------|-----------|--------------|
|1        |Australia  |110           |
|2        |America    |105           |
|3        |Africa     |102           |
|4        |Asia       |95            |
|5        |Europe     |88            |

### 4. How many days on average are customers reallocated to a different node?
```sql
WITH node_diff as(
SELECT
customer_id
,node_id
,start_date
,end_date
,end_date-start_date as date_diff
FROM data_bank.customer_nodes
WHERE end_date!= '9999-12-31'
ORDER BY customer_id,node_id
)

SELECT
ROUND(AVG(date_diff),0) AS avg_reallocation
FROM node_diff
```
#### Answer:
|avg_reallocation|
|----------------|
|15              |

### 5.What is the median, 80th and 95th percentile for this same reallocation days metric for each region?

Updating
