**3. Product Funnel Analysis**
----------------------

**Requirement:**

Using a single SQL query - create a new output table which has the following details:

* How many times was each product viewed?
* How many times was each product added to cart?
* How many times was each product added to a cart but not purchased (abandoned)?
* How many times was each product purchased?

**Query:**
```sql
WITH abandoned_cte AS
(
SELECT page_name, COUNT(e.event_type) AS abandoned
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
WHERE event_name = 'Add to Cart' AND visit_id NOT IN (SELECT visit_id FROM events WHERE event_type = 3)
GROUP BY page_name
),
purchased_cte AS
(
SELECT page_name, COUNT(e.event_type) AS purchased
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
WHERE event_name = 'Add to Cart' AND visit_id IN (SELECT visit_id FROM events WHERE event_type = 3)
GROUP BY page_name
)
SELECT p.page_name,
  SUM(CASE WHEN event_name = 'Page View' THEN 1 ELSE 0 END) AS product_views,
  SUM(CASE WHEN event_name = 'Add to Cart' THEN 1 ELSE 0 END) AS cart_adds,
  abandoned,
  purchased
INTO product_analysis
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
JOIN abandoned_cte a ON a.page_name = p.page_name
JOIN purchased_cte pu ON pu.page_name = p.page_name
WHERE product_id IS NOT NULL
GROUP BY p.page_name, abandoned, purchased
ORDER BY p.page_name;

SELECT * FROM product_analysis;
```

**Results:**

| page_name      | product_views   | cart_adds               | abandoned                 | purchased           |
| -------------- | --------------- | ----------------------- | ------------------------- | ------------------- |
| Abalone        | 1525            | 932                     | 233                       | 699                 |
| Black Truffle  | 1469            | 924                     | 217                       | 707                 |
| Crab           | 1564            | 949                     | 230                       | 719                 |
| Kingfish       | 1559            | 920                     | 213                       | 707                 |
| Lobster        | 1547            | 968                     | 214                       | 754                 |
| Oyster         | 1568            | 943                     | 217                       | 726                 |
| Russian Caviar | 1563            | 946                     | 249                       | 697                 |
| Salmon         | 1559            | 938                     | 227                       | 711                 |
| Tuna           | 1515            | 931                     | 234                       | 697                 |


----------

**Requirement:**

Additionally, create another table which further aggregates the data for the above points but this time for each product category instead of individual products.

Use your 2 new output tables - answer the following questions:

Which product had the most views, cart adds and purchases?
Which product was most likely to be abandoned?
Which product had the highest view to purchase percentage?
What is the average conversion rate from view to cart add?
What is the average conversion rate from cart add to purchase?

**Query:**
```sql
WITH abandoned_cte AS
(
SELECT product_category, COUNT(e.event_type) AS abandoned
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
WHERE event_name = 'Add to Cart' AND visit_id NOT IN (SELECT visit_id FROM events WHERE event_type = 3)
GROUP BY product_category
),
purchased_cte AS
(
SELECT product_category, COUNT(e.event_type) AS purchased
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
WHERE event_name = 'Add to Cart' AND visit_id IN (SELECT visit_id FROM events WHERE event_type = 3)
GROUP BY product_category
)
SELECT p.product_category,
  SUM(CASE WHEN event_name = 'Page View' THEN 1 ELSE 0 END) AS product_views,
  SUM(CASE WHEN event_name = 'Add to Cart' THEN 1 ELSE 0 END) AS cart_adds,
  abandoned,
  purchased
INTO category_analysis
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
JOIN abandoned_cte a ON a.product_category = p.product_category
JOIN purchased_cte pu ON pu.product_category = p.product_category
WHERE product_id IS NOT NULL
GROUP BY p.product_category, abandoned, purchased
ORDER BY p.product_category;

SELECT * FROM category_analysis;
```

**Results:**

| product_category | product_views   | cart_adds               | abandoned                 | purchased           |
| ---------------- | --------------- | ----------------------- | ------------------------- | ------------------- |
| Fish             | 4633            | 2789                    | 674                       | 2115                |
| Luxury           | 3032            | 1870                    | 466                       | 1404                |
| Shellfish        | 6204            | 3792                    | 894                       | 2898                |

--------------------------------------------

Use your 2 new output tables - answer the following questions:

**Question 1**
Which product had the most views, cart adds and purchases?

**Query**
```sql
WITH rank_cte AS
(
SELECT *, 
	   RANK() OVER(ORDER BY product_views DESC) AS view_rank,
	   RANK() OVER(ORDER BY cart_adds DESC) AS cart_adds_rank,
	   RANK() OVER(ORDER BY purchased DESC) AS purchase_rank
FROM product_analysis
)
SELECT page_name, product_views, cart_adds, purchased
FROM rank_cte
WHERE view_rank = 1 OR cart_adds_rank =1 OR purchase_rank =1;
```
**Results**
| page_name | product_views   | cart_adds               |  purchased          |
| -------- | --------------- | ----------------------- | ------------------- |
| Oyster    | 1568            | 943                     | 726                 |
| Lobster   | 1547            | 968                     | 754                 |

**Answer:**
Oyster and Lobster are products with the most number of views, cart adds, and purchases among all products
Even Oyster has more views, however, Lobster has more cart adds and purchases

------------------------------

**Question 2:**
Which product was most likely to be abandoned?

**Query**
```sql
WITH rank_cte AS
(
SELECT page_name, abandoned, RANK() OVER(ORDER BY abandoned DESC) AS abandon_rank
FROM product_analysis
)
SELECT page_name, abandoned
FROM rank_cte
WHERE abandon_rank =1;
```

**Results**
| page_name      | abandoned                 |
| -------------- | ------------------------- |
| Russian Caviar | 249                       |


**Answer:**
Russian Caviar is the most likely abandoned product

-----------------------------------

**Question 3:**
Which product had the highest view to purchase percentage?

**Query**
```sql
WITH rank_cte AS
(
SELECT page_name, purchase_view_percent, rank() OVER(ORDER BY purchase_view_percent DESC) AS rank
FROM product_analysis,
	LATERAL
	(SELECT ROUND(purchased/product_views::numeric*100, 1) AS purchase_view_percent) pvp
)
SELECT page_name, purchase_view_percent
FROM rank_cte
WHERE rank = 1;
```

In this query, I used `LATERAL` to enable a correlated subquery in the FROM clause, making the query shorter.

**Results:**

| page_name | purchase_view_percent       |
| --------- | --------------------------- |
| Lobster   | 48.7                        |

**Answer:**
Lobster is the product that had the highest view to purchase percentage.

----------------------------
**Question 4:**
What is the average conversion rate from view to cart add?

**Query:**
```sql
SELECT ROUND(SUM(cart_adds)/SUM(product_views)::numeric*100,2) AS avg_view_to_cart
FROM product_analysis;
```
**Results:**
| avg_view_to_cart |
| ----- |
| 60.93 |

**Answer:**
The average conversion rate from view to cart add is 60.93%

-------------------------

**Question 5:**
What is the average conversion rate from cart add to purchase?


**Query:**
```sql
SELECT ROUND(SUM(purchased)/SUM(cart_adds)::numeric*100,2) AS avg_cart_to_purchase
FROM product_analysis
```

**Results:**
| avg_cart_to_purchase |
| ----- |
| 75.93 |

**Answer:**
The average conversion rate from cart add to purchase is 75.93%


