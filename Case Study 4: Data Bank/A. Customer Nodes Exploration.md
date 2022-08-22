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


