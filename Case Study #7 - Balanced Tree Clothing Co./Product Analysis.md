**3. Product Analysis**
---------------

**Question 1:**
What are the top 3 products by total revenue before discount?

**Query:**
SELECT product_name, SUM(qty*s.price) AS revenue_before_discount
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY product_name
ORDER BY revenue_before_discount DESC
LIMIT 3;

**Result:**
| product_name                 | revenue_before_discount |
| ---------------------------- | ------- |
| Blue Polo Shirt - Mens       | 217683  |
| Grey Fashion Jacket - Womens | 209304  |
| White Tee Shirt - Mens       | 152000  |

------------------

**Question 2:**
What is the total quantity, revenue and discount for each segment?

**Query:**
```sql
SELECT segment_name, SUM(qty) AS quantity, SUM(qty*s.price) AS revenue, ROUND(SUM(discount*(qty*s.price)::numeric/100),1) AS discount
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY segment_name;
```

**Results:**

|segment_name|quantity|revenue|discount|
|------------|-------|-----------|------------|
|Shirt|11265|406143|49594.27|
|Jeans|11349|208350|25343.97|
|Jacket|11385|366983|44277.46|
|Socks|11217|307977|37013.44|

-----------
**Question 3:**
What is the top selling product for each segment?

**Query:**
```sql
WITH rank_cte AS
(
SELECT segment_name, product_name, SUM(qty) AS quantity_sold, RANK() OVER(PARTITION BY segment_name ORDER BY SUM(qty) DESC) AS selling_rank
FROM product_details p
JOIN sales s ON p.product_id = s.prod_id
GROUP BY 1,2
ORDER BY segment_name 
)
SELECT segment_name, product_name, quantity_sold
FROM rank_cte
WHERE selling_rank =1
ORDER BY quantity_sold DESC;;
```

**Results:**

| segment_name | product_name                  | quantity_sold  | 
| ------------ | ----------------------------- | -------------- | 
| Jacket       | Grey Fashion Jacket - Womens  | 3876           |
| Jeans        | Black Straight Jeans - Womens | 3786           |
| Shirt        | Blue Polo Shirt - Mens        | 3819           |
| Socks        | Navy Solid Socks - Mens       | 3792           |

-----------------------

**Question 4:**
What is the total quantity, revenue and discount for each category?

**Query:**

```sql
SELECT category_name, SUM(qty) AS quantity, SUM(qty*s.price) AS revenue, ROUND(SUM(discount*(qty*s.price)::numeric/100),1) AS discount
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY category_name;
```
**Results:**
|category_name|quantity|revenue|discount|
|-------------|-------|------------|-----------|
|Mens|22482|714120|86607.7|
|Womens|22734|575333|69621.4|

-------------------------
**Question 5:**
What is the top selling product for each category?

**Query:**
```sql
WITH rank_cte AS
(
SELECT category_name, product_name, SUM(qty) AS quantity_sold, RANK() OVER(PARTITION BY category_name ORDER BY SUM(qty) DESC) AS selling_rank
FROM product_details p
JOIN sales s ON p.product_id = s.prod_id
GROUP BY 1,2
ORDER BY category_name
)
SELECT category_name, product_name, quantity_sold
FROM rank_cte
WHERE selling_rank =1
ORDER BY quantity_sold DESC;
```

**Results:**

|category_name|product_name|quantity_sold|
|-------------|----------|----------|
|Womens|Grey Fashion Jacket - Womens|3876|
|Mens|Blue Polo Shirt - Mens|3819|

-----------------

**Question 6:**
What is the percentage split of revenue by product for each segment?

**Query:**
```sql
SELECT segment_name, product_name,
       ROUND(SUM(qty*s.price*(100-discount)::numeric/100),1) AS product_revenue,
	   ROUND(100*SUM(qty*s.price*(100-discount)::numeric/100)/SUM(SUM(qty*s.price*(100-discount)::numeric/100)) OVER(PARTITION BY segment_name),1) AS percentage
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY segment_name, product_name
ORDER BY segment_name
```
**Results:**
|segment_name|product_name|revenue|percentage|
|:----|:----|:----|:----|
|Jacket|Indigo Rain Jacket - Womens|62740.5|19.4|
|Jacket|Khaki Suit Jacket - Womens|76053.0|23.6|
|Jacket|Grey Fashion Jacket - Womens|183912.1|57.0|
|Jeans|Navy Oversized Jeans - Womens|43992.4|24.0|
|Jeans|Black Straight Jeans - Womens|106407.0|58.1|
|Jeans|Cream Relaxed Jeans - Womens|32606.6|17.8|
|Shirt|White Tee Shirt - Mens|133622.4|37.5|
|Shirt|Blue Polo Shirt - Mens|190863.9|53.5|
|Shirt|Teal Button Up Shirt - Mens|32062.4|9.0|
|Socks|Navy Solid Socks - Mens|119861.6|44.2|
|Socks|White Striped Socks - Mens|54724.2|20.2|
|Socks|Pink Fluro Polkadot Socks - Mens|96377.7|35.6|

-------------------

**Question 7:**
What is the percentage split of revenue by segment for each category?

**Query:**
```sql
SELECT category_name, segment_name,
       ROUND(SUM(qty*s.price*(100-discount)::numeric/100),1) AS segment_revenue,
	   ROUND(100*SUM(qty*s.price*(100-discount)::numeric/100)/SUM(SUM(qty*s.price*(100-discount)::numeric/100)) OVER(PARTITION BY category_name),1) AS percentage
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY category_name,segment_name;
```

**Results**
| category | segment | revenue | percentage |
| -------- | ------- | ------- | ----- |
| Mens     | Socks   | 307977  | 43.2 |
| Mens     | Shirt   | 406143  | 56.8 |
| Womens   | Jeans   | 208350  | 36.2 |
| Womens   | Jacket  | 366983  | 63.8 |

---------------
**Question 8:**
What is the percentage split of total revenue by category?

**Query**
```sql
SELECT category_name, 
	   ROUND(SUM((qty*s.price)*(100-discount)::numeric/100),1) AS revenue,
	   ROUND(100*SUM((qty*s.price)*(100-discount)::numeric/100)/SUM(SUM((qty*s.price)*(100-discount)::numeric/100)) OVER(),1) AS percentage
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY category_name
```

**Results:**
| category | revenue | percentage|
| -------- | ------- | ----- |
| Mens     | 714120  | 55.4 |
| Womens   | 575333  | 44.6 |

-----------------

**Question 9:**
What is the total transaction “penetration” for each product? 
(hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions)

**Query**
```sql
WITH product_cte AS
(
SELECT product_name, COUNT(DISTINCT txn_id) AS product_transactions
FROM sales s
JOIN product_details p ON p.product_id = s.prod_id
GROUP BY product_name
),
transaction_cte AS
(
SELECT COUNT(DISTINCT txn_id) AS total_transactions
FROM sales
)
SELECT product_name, ROUND(product_transactions/total_transactions::numeric*100,1) AS penetration_percent
FROM product_cte, transaction_cte
ORDER BY 2 DESC;
```

**Results:**
| product_name                     | penetration_percent |
| -------------------------------- | ------------------- |
| Navy Solid Socks - Mens          | 51.24               |
| Grey Fashion Jacket - Womens     | 51.00               |
| Navy Oversized Jeans - Womens    | 50.96               |
| White Tee Shirt - Mens           | 50.72               |
| Blue Polo Shirt - Mens           | 50.72               |
| Pink Fluro Polkadot Socks - Mens | 50.32               |
| Indigo Rain Jacket - Womens      | 50.00               |
| Khaki Suit Jacket - Womens       | 49.88               |
| Black Straight Jeans - Womens    | 49.84               |
| Cream Relaxed Jeans - Womens     | 49.72               |
| White Striped Socks - Mens       | 49.72               |
| Teal Button Up Shirt - Mens      | 49.68               |

-------------------

**10. What is the most common combination of at least 1 quantity of any 3 products in a 1 single transaction?**

[Work in progress]

