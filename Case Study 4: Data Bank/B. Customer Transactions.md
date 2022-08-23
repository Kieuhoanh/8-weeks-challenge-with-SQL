# 🏦 CASE STUDY 4: DATA BANK
## 💰B. Customer Transactions
### 1. What is the unique count and total amount for each transaction type?
```sql
SELECT
txn_type
,COUNT(*)
,SUM(txn_amount) AS total_amount
FROM data_bank.customer_transactions
GROUP BY txn_type
```
#### Answer:
|txn_type   |count|total_amount|
|-----------|-----|------------|
|purchase   |115  |806537      |
|withdrawal |108  |793003      |
|deposit    |113  |1359168     |

### 2. What is the average total historical deposit counts and amounts for all customers?
- Firstly, find the count of transaction and average transaction amount for each customer where the transaction type is deposit.
- Then, find the average of both columns .
```sql
WITH t AS ( 
SELECT
customer_id
,txn_type
,COUNT (*) count_transaction
,AVG(txn_amount) AS total_amount
FROM data_bank.customer_transactions
WHERE txn_type='deposit'
GROUP BY customer_id,txn_type
)
SELECT 
ROUND(AVG(count_transaction),0) avg_count
,ROUND(AVG(total_amount),0) avg_amount
FROM t
```
#### Answer:
|avg_count|avg_amount|
|---------|----------|
|5        |115       |

### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?

````SQL
WITH monthly_transactions AS (
  SELECT 
    customer_id 
    ,DATE_PART('month', txn_date) AS month
    ,SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) AS deposit_count
    ,SUM(CASE WHEN txn_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count
    ,SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count
  FROM data_bank.customer_transactions
  GROUP BY customer_id, month
 )

SELECT
  month
  ,COUNT(DISTINCT customer_id) AS customer_count
FROM monthly_transactions
WHERE deposit_count >= 2 
  AND (purchase_count = 1 OR withdrawal_count = 1)
GROUP BY month
ORDER BY month;
````
#### Answer:
|month|customer_count|
|-----|--------------|
|1    |115           |
|2    |108           |
|3    |113           |
|4    |50            |

### 4. What is the closing balance for each customer at the end of the month?
