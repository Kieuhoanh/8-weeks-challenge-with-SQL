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
```sql
 -- CTE 1 - To identify transaction amount as an inflow (+) or outflow (-)
WITH monthly_balances AS (
SELECT 
customer_id 
,(DATE_TRUNC('month', txn_date) + INTERVAL '1 MONTH - 1 DAY') AS closing_month 
,SUM(CASE WHEN txn_type = 'withdrawal' OR txn_type = 'purchase' THEN (-txn_amount)
ELSE txn_amount END) AS transaction_balance
FROM data_bank.customer_transactions
GROUP BY customer_id, closing_month
),

--CTE 2 - To generate txn_date as a series of last day of month for each customer
last_day AS (
SELECT
DISTINCT customer_id
,('2020-01-31'::DATE + GENERATE_SERIES(0,3) * INTERVAL '1 MONTH') AS ending_month
FROM data_bank.customer_transactions
)

-- Create closing balance for each month using Window function SUM() to add changes during the month
SELECT 
ld.customer_id 
,ld.ending_month
,COALESCE(mb.transaction_balance, 0) AS monthly_change
,SUM(mb.transaction_balance) OVER 
(PARTITION BY ld.customer_id ORDER BY ld.ending_month) AS closing_balance
FROM last_day ld
LEFT JOIN monthly_balances mb
ON ld.ending_month = mb.closing_month
AND ld.customer_id = mb.customer_id
```
#### Answer:
|customer_id  |ending_month|monthly_change|closing_balance|
|-------------|------------|--------------|---------------|
|1            |2020-01-31  |312           |312            |
|1            |2020-02-29  |0             |312            |
|1            |2020-03-31  |-952          |-640           |
|1            |2020-04-30  |0             |-640           |
|2            |2020-01-31  |549           |549            |
|2            |2020-02-29  |0             |549            |
|2            |2020-03-31  |61            |610            |
|2            |2020-04-30  |0             |610            |
