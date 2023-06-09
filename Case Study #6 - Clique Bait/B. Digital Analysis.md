**2. Digital Analysis**
---
Using the available datasets - answer the following questions using a single query for each one:

**Question 1:**
How many users are there?

**Query:**

```sql
SELECT COUNT(DISTINCT(user_id)) as customer_count
FROM users;
```

**Results:**

|customer_count|
|---------|
|500|

* There are 500 users

---------------------------

**Question 2**
How many cookies does each user have on average?

**Query:**
```sql
WITH cookie_count_cte AS
(
SELECT user_id, COUNT(cookie_id) AS cookie_per_customer
FROM users
GROUP BY user_id
)
SELECT ROUND(AVG(cookie_per_customer)) AS avg_cookie
FROM cookie_count_cte;
```

**Results:**

| avg_cookie  |
| ----------- |
| 4 |

**Answer:**
On average, each user have 4 cookies.

-------------
**Question 3**
What is the unique number of visits by all users per month?

**Query:**

```sql
SELECT TO_CHAR(event_time, 'Month') AS month, COUNT (DISTINCT visit_id) AS unique_count
FROM events
GROUP BY 1, EXTRACT('month' FROM event_time)
ORDER BY EXTRACT('month' FROM event_time);
```

**Results:**

| month     | unique_count |
| --------- | ----- |
| January   | 876   |
| February  | 1488  |
| March     | 916   |
| April     | 248   |
| May       | 36    |

--------------------

**Question 5**
What is the percentage of visits which have a purchase event?

**Query:**

```sql
SELECT ROUND(COUNT(visit_id)::numeric/(SELECT COUNT(DISTINCT visit_id) FROM events)::numeric*100,1) AS percentage
FROM events e
JOIN event_identifier i ON e.event_type = i.event_type
WHERE event_name ='Purchase';
```
To count the percentage of purchase events, I count the `visit_id` that have a purchase event, divided it to the amount of distinct `visit_id` and multiply by 100.

**Results:**

| percentage |
| ----- |
| 49.9 |

-------------------
**Question 6:**
What is the percentage of visits which view the checkout page but do not have a purchase event?

**Query:**
```sql
WITH event_cte AS (
SELECT 
  visit_id,
  CASE WHEN event_type = 1 AND page_id = 12 THEN 1 ELSE 0 END AS checkout,
  CASE WHEN event_type = 3 THEN 1 ELSE 0 END AS purchase
FROM clique_bait.events
)
SELECT 
  ROUND(100 * (1-(SUM(purchase)::numeric/SUM(checkout))),1) AS percentage
FROM event_cte
```
I use `CASE WHEN` statement in `event_cte` to find out visits that view the checkout page and visits that have a purchase event. With the information from the created cte, we can find the requested percentage.

**Results:**
| percentage |
| ----- |
| 15.5 |

--------------------
**Question 7**
What are the top 3 pages by number of views?

**Query :**

```sql
SELECT page_name, COUNT(e.event_type) AS view_count
FROM events e
JOIN event_identifier i ON e.event_type = i.event_type
JOIN page_hierarchy p ON p.page_id = e.page_id
WHERE event_name = 'Page View'
GROUP BY page_name
ORDER BY view_count DESC
LIMIT 3;
```

I first joined table `events` with tables `event_identifier` and `page_hierarchy` to find the `view_count` of each `page_name`. The results set are ordered by the `view_count` in descending order and limit to 3 to find three most viewed page.

**Results:**

| page_name    | view_count |
| ------------ | ------------ |
| All Products | 3174         |
| Checkout     | 2103         |
| Home Page    | 1782         |

-----------------------------

**Question 8:**
What is the number of views and cart adds for each product category?

**Query:**
```sql
SELECT product_category,
  SUM(CASE WHEN event_name='Page View' THEN 1 ELSE 0 END) AS number_of_views,
  SUM(CASE WHEN event_name='Add to Cart' THEN 1 ELSE 0 END) AS numer_of_cart_adds
FROM events e
JOIN event_identifier i ON e.event_type = i.event_type
JOIN page_hierarchy p ON p.page_id = e.page_id
WHERE product_category IS NOT NULL
GROUP BY product_category
ORDER BY number_of_views DESC;
```

I use `CASE WHEN` statement to find out the `number_of_views` and `number_of_cart_adds` of each `product_category`. If the `event_name` is 'Page View` then we count it as 1, else we count it as 0. Then we can get the sum result group by `producategory`. We do it similiarly to find the number of cart adds, too.
There are product with NULL values so we use WHERE statement to avoid those results.

**Results:**
| product_category | number_of_views | number_of_cart_adds |
| ---------------- | ------------ | ---------------- |
| Shellfish        | 6204         | 3792             |
| Fish             | 4633         | 2789             |
| Luxury           | 3032         | 1870             |

-------------------------------------------

**Question 9:**
What are the top 3 products by purchases?

**Query:**
```sql
SELECT page_name, COUNT(e.event_type) AS purchases
FROM events e
JOIN page_hierarchy p ON e.page_id = p.page_id
JOIN event_identifier i ON i.event_type = e.event_type
WHERE product_category IS NOT NULL AND event_name = 'Add to Cart' AND visit_id IN (SELECT visit_id FROM events WHERE event_type = 3)
GROUP BY page_name
ORDER BY purchases DESC
LIMIT 3;
```
Similar to question 7, I joined tables `events`, `event_identifier`, and `page_hierarchy` to filter the values easier. Here `page_name` acts like a product name, so I filter out any `page_name` that doesn't have a `product_category`. Next, I applied conditions on `event_name` and `visit_id` to only count the products which was added to cart and purchased eventually.

**Results:**
| page_name | purchases |
| --------- | --------- |
| Lobster   | 754       |
| Oyster    | 726       |
| Crab      | 719       |
