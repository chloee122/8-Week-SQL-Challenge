**2. Transaction Analysis**
-------------

**Question 1:**
How many unique transactions were there?

**Query**
```sql
SELECT COUNT(DISTINCT txn_id) 
FROM sales;
```

**Results**
|count|
|---------|
|2500|

**Answer:**
There are 2500 unique transactions

**Question 2:**
What is the average unique products purchased in each transaction?


**Query:**
```sql
WITH purchase_cte AS
(
SELECT txn_id, COUNT(prod_id) AS purchased_product
FROM sales
GROUP BY 1
)
SELECT ROUND(AVG(purchased_product)) AS avg_product_purchased
FROM purchase_cte;
```

**Results:**
|avg_product_purchased|
|---------|
|6|

**Answer:**
On average, there are 6 unique product purchased in each transaction.

--------------

What are the 25th, 50th and 75th percentile values for the revenue per transaction?


**Query:**
```sql
WITH revenue_cte AS
(
SELECT txn_id, ROUND(SUM(qty*price*(100-discount)::numeric/100)) AS revenue
FROM sales
GROUP BY txn_id
)
SELECT PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY revenue ASC) AS percentile_25,
       PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY revenue ASC) AS percentile_50,
       PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY revenue ASC) AS percentile_75
FROM revenue_cte;
```
In CTE `revenue_cte` I calculated the revenue after discount of each transaction.
To find the remaining sales after discount, I used 100 - discount. If the discount amount is 17%, then the remaining sales is equal to 100 - 17.

**Results:**

| percentile_25 | percentile_50 | percentile_75 |
| ------------- | ------------- | ------------- |
| 326           | 441           | 573           |

-----------------------------


