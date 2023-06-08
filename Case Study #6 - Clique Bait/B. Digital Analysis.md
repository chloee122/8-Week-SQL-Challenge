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
I use `case` expression in `event_cte` to find out visits that view the checkout page and visits that have a purchase event. With the information from the created cte, we can find the requested percentage.

**Results:**
| percentage |
| ----- |
| 15.5 |

