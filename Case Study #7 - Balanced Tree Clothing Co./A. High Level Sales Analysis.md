**1. High Level Sales Analysis**
---------------------

**Question 1:**
What was the total quantity sold for all products?


**Query:**

```sql
SELECT product_name, SUM(qty) AS total_quantity
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY product_name
```

**Results:**
|product_name|total_quantity|
|:----|:----|
|White Tee Shirt - Mens|3800|
|Navy Solid Socks - Mens|3792|
|Grey Fashion Jacket - Womens|3876|
|Navy Oversized Jeans - Womens|3856|
|Pink Fluro Polkadot Socks - Mens|3770|
|Khaki Suit Jacket - Womens|3752|
|Black Straight Jeans - Womens|3786|
|White Striped Socks - Mens|3655|
|Blue Polo Shirt - Mens|3819|
|Indigo Rain Jacket - Womens|3757|
|Cream Relaxed Jeans - Womens|3707|
|Teal Button Up Shirt - Mens|3646|

--------------------

**Question 2:**
What is the total generated revenue for all products before discounts?

**Query:**

```sql
SELECT product_name, SUM(qty*s.price) AS sales
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY product_name;
```
**Results:**

|product_name|sales|
|:----|:----|
|White Tee Shirt - Mens|152000|
|Navy Solid Socks - Mens|136512|
|Grey Fashion Jacket - Womens|209304|
|Navy Oversized Jeans - Womens|50128|
|Pink Fluro Polkadot Socks - Mens|109330|
|Khaki Suit Jacket - Womens|86296|
|Black Straight Jeans - Womens|121152|
|White Striped Socks - Mens|62135|
|Blue Polo Shirt - Mens|217683|
|Indigo Rain Jacket - Womens|71383|
|Cream Relaxed Jeans - Womens|37070|
|Teal Button Up Shirt - Mens|36460|

-------------------------
**Question 3:**
What was the total discount amount for all products?

**Query:**
```sql
SELECT product_name, ROUND(SUM(qty*s.price*s.discount::numeric/100),1) AS discount_amount
FROM product_details p
JOIN sales s ON s.prod_id = p.product_id
GROUP BY product_name
```
**Results:**

|product_name| discount_amount|
|:----|:----|
|White Tee Shirt - Mens|18377.6|
|Navy Solid Socks - Mens|16650.4|
|Grey Fashion Jacket - Womens|25391.9|
|Navy Oversized Jeans - Womens|6135.6|
|Pink Fluro Polkadot Socks - Mens|12952.3|
|Khaki Suit Jacket - Womens|10243.1|
|Black Straight Jeans - Womens|14745.0|
|White Striped Socks - Mens|7410.8|
|Blue Polo Shirt - Mens|26819.1|
|Indigo Rain Jacket - Womens|8642.5|
|Cream Relaxed Jeans - Womens|4463.4|
|Teal Button Up Shirt - Mens|4397.6|

------------------

