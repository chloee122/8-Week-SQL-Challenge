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
