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
**Question 4:**
What is the average discount value per transaction?

**Query:**

```sql
WITH discount_cte AS
(
SELECT txn_id, ROUND(SUM(qty*price*discount::numeric/100),1) AS discount_amount
FROM sales
GROUP BY txn_id
)
SELECT ROUND(AVG(discount_amount),1) AS avg_discount_amount
FROM discount_cte;
```
In the `discount_cte`, I calculated the `sum` discount amount `group by` each transaction.
I then calculated the average discount amount in the main query.

**Results**

| avg_discount_amount
| ------------ |
| 62.5         |

--------------------------

**Question 5:**
What is the percentage split of all transactions for members vs non-members?

**Query:**

```sql
WITH member_cte AS
(
SELECT DISTINCT txn_id,
	   COUNT(DISTINCT txn_id) AS total_transaction,
	   CASE WHEN member = TRUE THEN 1 ELSE 0 END AS members,
	   CASE WHEN member = FALSE THEN 1 ELSE 0 END AS non_members
FROM sales
GROUP BY txn_id, members, non_members
)
SELECT ROUND(SUM(members)/SUM(total_transaction)*100,1) AS member_percentage,
       ROUND(SUM(non_members)/SUM(total_transaction)*100,1) AS non_member_percentage
FROM member_cte;
```
In `member_cte`, I found the unique transactions and count them. I also use `CASE WHEN` to mark values made by members and non_members
In the main query, I summed the unique transactions, calculated the number of members and non-members, and found the percentage of transactions made by the member and the non-members.

-----------------

**Question 6:**
What is the average revenue for member transactions and non-member transactions?

**Query**
```sql
WITH revenue_cte AS
(
SELECT DISTINCT txn_id,
	  SUM(CASE WHEN member = TRUE THEN ROUND(qty*price*(100-discount)::numeric/100,1) END) AS member_revenue,
	  SUM(CASE WHEN member = FALSE THEN ROUND(qty*price*(100-discount)::numeric/100,1) END) AS non_member_revenue
FROM sales
GROUP BY txn_id
)
SELECT ROUND(AVG(member_revenue),1) AS avg_member_revenue, ROUND(AVG(non_member_revenue),1) AS avg_non_member_revenue
FROM revenue_cte;
```
In `revenue_cte`, I calculated the revenue per each member transaction and non-member transaction by using `CASE WHEN` and `SUM`
In the main query, I found the average revenue for member transactions and non-member transactions.

**Results:**

| avg_member_revenue | avg_non_member_revenue |
| ------------------ | ---------------------- |
| 454.2              | 452.0                  |

