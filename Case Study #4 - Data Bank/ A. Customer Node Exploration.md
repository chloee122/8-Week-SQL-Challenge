-- A. CUSTOMER NODES EXPLORATION
--- How many unique nodes are there on the Data Bank system?
-- SELECT COUNT(DISTINCT node_id)
-- FROM customer_nodes;

--- What is the number of nodes per region?
-- SELECT region_name, COUNT(node_id)
-- FROM customer_nodes node 
-- JOIN regions re ON re.region_id = node.region_id
-- GROUP BY region_name;

-- How many customers are allocated to each region?
-- SELECT region_name, COUNT(DISTINCT customer_id) AS number_of_customers
-- FROM customer_nodes node
-- JOIN regions re ON re.region_id = node.region_id
-- GROUP BY region_name;

-- How many days on average are customers reallocated to a different node?
-- WITH days_cte AS
-- (
-- SELECT customer_id, node_id,
--   CASE WHEN node_id <> LEAD(node_id) OVER(PARTITION BY customer_id ORDER BY start_date)
--   	   THEN LEAD(start_date) OVER(PARTITION BY customer_id ORDER BY start_date) - start_date
-- 	   END AS allocation_days
-- FROM customer_nodes
-- )
-- SELECT ROUND(AVG(allocation_days),1)
-- FROM days_cte;

-- What is the median, 80th and 95th percentile for this same reallocation days metric for each region?
-- WITH days_cte AS
-- (
-- SELECT re.region_name, node.customer_id, node.node_id, 
--   CASE WHEN node_id <> LEAD(node_id) OVER(PARTITION BY customer_id ORDER BY start_date)
--   	   THEN LEAD(start_date) OVER(PARTITION BY customer_id ORDER BY start_date) - start_date
-- 	   END AS allocation_days
-- FROM customer_nodes node
-- JOIN regions re ON re.region_id = node.region_id
-- )
-- SELECT region_name, 
-- 	   percentile_cont(0.5) within group (ORDER BY allocation_days ASC) AS median,
-- 	   percentile_cont(0.8) within group (ORDER BY allocation_days ASC) AS percentile_80,
-- 	   percentile_cont(0.95) within group (ORDER BY allocation_days ASC) AS percentile_95
-- FROM days_cte
-- GROUP BY region_name;

-- B. CUSTOMER TRANSACTIONS
-- What is the unique count and total amount for each transaction type?
-- SELECT txn_type, COUNT(txn_amount) AS transaction_times
-- FROM customer_transactions
-- GROUP BY txn_type;

--What is the average total historical deposit counts and amounts for all customers?
-- WITH transaction_info AS
-- (
-- SELECT customer_id, COUNT(txn_type) AS deposit_time, SUM(txn_amount) AS total_amount
-- FROM customer_transactions
-- WHERE txn_type ='deposit'
-- GROUP BY customer_id
-- )
-- SELECT ROUND(AVG(deposit_time)) AS avg_times,ROUND(AVG(total_amount),1) AS avg_amount
-- FROM transaction_info;

-- For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
-- WITH deposit AS
-- (
-- SELECT customer_id, DATE_TRUNC('month',txn_date) AS txn_date, COUNT(txn_type) AS deposit_count
-- FROM customer_transactions
-- WHERE txn_type ='deposit'
-- GROUP BY customer_id, txn_date
-- HAVING COUNT(txn_type)>1
-- ),
-- other AS
-- (
-- SELECT customer_id, DATE_TRUNC('month',txn_date) AS txn_date, COUNT(txn_type) AS others_count
-- FROM customer_transactions
-- WHERE txn_type IN ('withdraw', 'purchase')
-- GROUP BY customer_id, txn_date
-- HAVING COUNT(txn_type) >=1
-- )
-- SELECT de.txn_date AS months, COUNt(*)
-- FROM deposit de
-- JOIN other o ON o.customer_id = de.customer_id AND o.txn_date = de.txn_date
-- GROUP BY de.txn_date;

--What is the closing balance for each customer at the end of the month?
WITH transactions AS
(
SELECT customer_id, DATE_TRUNC('month',txn_date) AS months,
   SUM(CASE WHEN txn_type='deposit' THEN txn_amount
   ELSE -txn_amount
   END) AS balance
FROM customer_transactions
GROUP BY customer_id, months
ORDER BY customer_id, months
)
SELECT customer_id, generate_series(months,
	CASE WHEN LEAD(months) OVER(PARTITION BY customer_id) IS NOT NULL THEN LEAD(months) OVER(PARTITION BY customer_id)
	ELSE '2020-05-01'
	END, '1 month'+'1 minute'::interval) AS months, 
	CASE WHEN LAG(balance,1) OVER(PARTITION BY customer_id ORDER BY months) <> balance THEN (balance + LAG(balance,1) OVER(PARTITION BY customer_id ORDER BY months))
	ELSE balance
	END AS balance
FROM transactions;

SELECT * FROM customer_transactions
WHERE customer_id = 5
ORDER BY txn_date;


SELECT customer_id, DATE_TRUNC('month',txn_date) AS months,
   SUM(CASE WHEN txn_type='deposit' THEN txn_amount
   ELSE -txn_amount
   END) AS balance
FROM customer_transactions
WHERE customer_id = 5
GROUP BY customer_id, months
ORDER BY customer_id, months;

