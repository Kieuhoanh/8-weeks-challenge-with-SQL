

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
