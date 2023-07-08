B. Customer Transactions
-----------------

**QUESTION 1:**
What is the unique count and total amount for each transaction type?

**Query:**
```sql
SELECT txn_type, COUNT(txn_amount) AS transaction_times, SUM(txn_amount) AS total_amount 
FROM customer_transactions
GROUP BY txn_type;
```

**Results:**

| txn_type   | transaction_times | total_amount |
| ---------- | ----------------- | ------------ |
| purchase   | 1580              | 806537       |
| withdrawal | 1617              | 793003       |
| deposit    | 2671              | 1359168      |

-------------------

**QUESTION 2:**
What is the average total historical deposit counts and amounts for all customers?

**Query:**
```sql
WITH transaction_info AS
(
SELECT customer_id, COUNT(txn_type) AS deposit_time, SUM(txn_amount) AS total_amount
FROM customer_transactions
WHERE txn_type ='deposit'
GROUP BY customer_id
)
SELECT ROUND(AVG(deposit_time)) AS avg_times,ROUND(AVG(total_amount),1) AS avg_amount
FROM transaction_info;
```

**Results:**

| avg_times | avg_amount |
| ----------------- | --------------- |
| 5                 | 2718.3          |

The average deposit counts is 5 and the average amount per deposit is 2718.3

--------------------

**QUESTION 3:**
For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?

**Query:**
```sql
WITH deposit AS
(
SELECT customer_id, DATE_TRUNC('month',txn_date) AS txn_date, COUNT(txn_type) AS deposit_count
FROM customer_transactions
WHERE txn_type ='deposit'
GROUP BY customer_id, txn_date
HAVING COUNT(txn_type)>1
),
other AS
(
SELECT customer_id, DATE_TRUNC('month',txn_date) AS txn_date, COUNT(txn_type) AS others_count
FROM customer_transactions
WHERE txn_type IN ('withdraw', 'purchase')
GROUP BY customer_id, txn_date
HAVING COUNT(txn_type) >=1
)
SELECT de.txn_date AS months, COUNt(*)
FROM deposit de
JOIN other o ON o.customer_id = de.customer_id AND o.txn_date = de.txn_date
GROUP BY de.txn_date;
```



