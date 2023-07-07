A. Customer Nodes Exploration
---------------------

**Question 1:**
How many unique nodes are there on the Data Bank system?

**Query:**
```sql
SELECT COUNT(DISTINCT node_id) As node_count
FROM customer_nodes;
```

**Results:**

| node_count |
| ----- |
| 5     |

---------------------
**Question 2:**
What is the number of nodes per region?

**Query:**

```sql
SELECT region_name, COUNT(node_id) AS node_count
FROM customer_nodes node 
JOIN regions re ON re.region_id = node.region_id
GROUP BY region_name;
```

**Results:**
| region    | node_count |
| --------- | --------------- |
| America   | 735             |
| Australia | 770             |
| Africa    | 714             |
| Asia      | 665             |
| Europe    | 616             |

------------------------

**Question 3:**
How many customers are allocated to each region?

**Query:**
```sql
SELECT region_name, COUNT(DISTINCT customer_id) AS number_of_customers
FROM customer_nodes node
JOIN regions re ON re.region_id = node.region_id
GROUP BY region_name;
```
**Results:**

| region    | number_of_customers |
| --------- | ---------------- |
| Africa    | 102              |
| America   | 105              |
| Asia      | 95               |
| Australia | 110              |
| Europe    | 88               |

------------------------

**Question 4:**
How many days on average are customers reallocated to a different node?

**Query:**
```sql
WITH days_cte AS
(
SELECT customer_id, node_id,
  CASE WHEN node_id <> LEAD(node_id) OVER(PARTITION BY customer_id ORDER BY start_date)
  	   THEN LEAD(start_date) OVER(PARTITION BY customer_id ORDER BY start_date) - start_date
	   END AS allocation_days
FROM customer_nodes
)
SELECT ROUND(AVG(allocation_days),1) AS avg_allocation_days
FROM days_cte;
```

**Results:**
|avg_reallocation_days |
| ------------------------- |
| 15.6      |

**Answer:**
On average, customers are reallocated to a different node after 16 days.

--------------
**Question 5:**
What is the median, 80th and 95th percentile for this same reallocation days metric for each region?

**Query:**
```sql
WITH days_cte AS
(
SELECT re.region_name, node.customer_id, node.node_id, 
  CASE WHEN node_id <> LEAD(node_id) OVER(PARTITION BY customer_id ORDER BY start_date)
  	   THEN LEAD(start_date) OVER(PARTITION BY customer_id ORDER BY start_date) - start_date
	   END AS allocation_days
FROM customer_nodes node
JOIN regions re ON re.region_id = node.region_id
)
SELECT region_name, 
	   percentile_cont(0.5) within group (ORDER BY allocation_days ASC) AS median,
	   percentile_cont(0.8) within group (ORDER BY allocation_days ASC) AS percentile_80,
	   percentile_cont(0.95) within group (ORDER BY allocation_days ASC) AS percentile_95
FROM days_cte
GROUP BY region_name;
```

**Results:**

| region_name  | median | percentile_80 | percentile_95 |
| --------- | ------ | ---------------------- | ----------------------- |
| Africa    | 16     | 25                     | 29                      |
| America   | 16     | 24                     | 29                      |
| Asia      | 15     | 24                     | 29                      |
| Australia | 15.5   | 24                     | 29                      |
| Europe    | 16     | 26                     | 29                      |







