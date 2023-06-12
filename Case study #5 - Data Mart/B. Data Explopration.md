**2. Data Exploration**
--------------------

**Question 1:**
What day of the week is used for each week_date value?

**Query:**

```sql
SELECT DISTINCT(TO_CHAR(week_date, 'Day')) AS Day
FROM clean_weekly_sales;
```

I used `TO_CHAR` to get the Day format of the week_date, then I used `DISTINCT` to get the unique results.

**Results:**

| Day     |
| --------- |
| Monday    |

**Answer**
Monday is the day used for each week_date value

---------------------------------------------------

**Question 2:**
What range of week numbers are missing from the dataset?


**Query:**

```sql
With week_number_cte AS
(
SELECT GENERATE_SERIES(1,52) AS week_number
)
SELECT ARRAY_AGG(w.week_number) AS missing_week_number
FROM clean_weekly_sales c
RIGHT JOIN week_number_cte w ON c.week_number = w.week_number
WHERE c.week_number IS NULL;
```
There are 52 weeks in a year so I generated a series from 1 to 52. 
To find the missing week_number, I use `RIGHT JOIN` to join `clean_weekly_sales` and `week_number_cte`.
Any week_number value of `clean_weekly_sales` that doesn't match with week_number value of `missing_week_number`  will return `NULL`. 
Hence, we can use a WHERE clause to filter NULL values and find the `missing_week_number`.


**Results:**

| missing_week_number                                                        |
| -------------------------------------------------------------------------- |
| 1,2,3,4,5,6,7,8,9,10,11,12,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52 |

-----------------------

**Question 3:**
How many total transactions were there for each year in the dataset?


**Query:**

```sql
SELECT calendar_year as year, COUNT(transactions) AS total_transaction
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY year;
```

**Results:**

| year | total_transaction |
| ---- | ----------- |
| 2018 | 5698        |
| 2019 | 5708        |
| 2020 | 5711        |

----------------------

**Question 4:**
What is the total sales for each region for each month?


**Query:**

```sql
SELECT region, calendar_year, TO_CHAR(week_date,'month') as month, SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY region, calendar_year, month, month_number
ORDER BY region, calendar_year, month_number;
```

**Results:**

Here is the partial output

| region        | calendar_year | month     | total_sales |
| ------------- | ------------- | --------- | --------- |
| AFRICA        | 2018          | march     | 130542213 |
| AFRICA        | 2018          | april     | 650194751 |
| AFRICA        | 2018          | may       | 522814997 |
| AFRICA        | 2018          | june      | 519127094 |
| AFRICA        | 2018          | july      | 674135866 |
| AFRICA        | 2018          | august    | 539077371 |
| AFRICA        | 2018          | september | 135084533 |
| AFRICA        | 2019          | march     | 141619349 |
| AFRICA        | 2019          | april     | 700447301 |
| AFRICA        | 2019          | may       | 553828220 |
| AFRICA        | 2019          | june      | 546092640 |
| AFRICA        | 2019          | july      | 711867600 |
| AFRICA        | 2019          | august    | 564497281 |

-----------------

**Question 5:**
What is the total count of transactions for each platform

**Query:**
```sql
SELECT platform, COUNT(transactions)
FROM clean_weekly_sales
GROUP BY platform
``` 

**Results:**

| platform | count |
| -------- | ----- |
| Shopify  | 8549  |
| Retail   | 8568  |

-------------------

**Question 6:**
What is the percentage of sales for Retail vs Shopify for each month?
-----

**Query:**

```sql
WITH platform_sales_cte AS 
(
SELECT calendar_year, 
	   TO_CHAR(week_date,'month') AS month, 
	   SUM(sales) AS sales,
	   SUM(CASE WHEN platform ='Retail' THEN sales ELSE 0 END) AS retail_sales,
	   SUM(CASE WHEN platform ='Shopify' THEN sales ELSE 0 END) AS shopify_sales
FROM clean_weekly_sales
GROUP BY calendar_year, month, month_number
ORDER BY calendar_year, month_number
)
SELECT calendar_year,
       month, 
	   ROUND((retail_sales::numeric/sales::numeric)*100,2) AS retail_sales_percentage,
	   ROUND((shopify_sales::numeric/sales::numeric)*100,2) AS shopify_sales_percentage
FROM platform_sales_cte;
```

**Results:**

| calendar_year | month     | retail_sales_percentage | shopify_sales_percentage |
| ---- | --------- | ----------------------- | ------------------------ |
| 2018 | march     | 97.92                   | 2.08                     |
| 2018 | april     | 97.93                   | 2.07                     |
| 2018 | may       | 97.73                   | 2.27                     |
| 2018 | june      | 97.76                   | 2.24                     |
| 2018 | july      | 97.75                   | 2.25                     |
| 2018 | august    | 97.71                   | 2.29                     |
| 2018 | september | 97.68                   | 2.32                     |
| 2019 | march     | 97.71                   | 2.29                     |
| 2019 | april     | 97.80                   | 2.20                     |
| 2019 | may       | 97.52                   | 2.48                     |
| 2019 | june      | 97.42                   | 2.58                     |
| 2019 | july      | 97.35                   | 2.65                     |
| 2019 | august    | 97.21                   | 2.79                     |
| 2019 | september | 97.09                   | 2.91                     |
| 2020 | march     | 97.30                   | 2.70                     |
| 2020 | april     | 96.96                   | 3.04                     |
| 2020 | may       | 96.71                   | 3.29                     |
| 2020 | june      | 96.80                   | 3.20                     |
| 2020 | july      | 96.67                   | 3.33                     |
| 2020 | august    | 96.51                   | 3.49                     |

-------------------------
**Question 7**
What is the percentage of sales by demographic for each year in the dataset?

**Query:**
```sql
WITH demographic_sales_cte AS
(
SELECT calendar_year,
       SUM(sales) AS total_sales, 
       SUM(CASE WHEN demographic = 'Couples' THEN sales ELSE 0 END) AS couple_customer_sales,
	   SUM(CASE WHEN demographic = 'Families' THEN sales ELSE 0 END) AS family_customer_sales,
	   SUM(CASE WHEN demographic = 'Unknown' THEN sales ELSE 0 END) AS unknown_customer_sales
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year
)
SELECT calendar_year, 
       ROUND((couple_customer_sales::numeric/total_sales::numeric)*100,2) AS couple_sales_percent,
	   ROUND((family_customer_sales::numeric/total_sales::numeric)*100,2) AS family_sales_percent,
       ROUND((unknown_customer_sales::numeric/total_sales::numeric)*100,2) AS unknown_sales_percent
FROM demographic_sales_cte;
```

**Results:**

| year | couples_sales_percent | fam_sales_percent | unknown_sales_percent |
| ---- | --------------------- | ----------------- | --------------------- |
| 2018 | 26.38                 | 31.99             | 41.63                 |
| 2019 | 27.28                 | 32.47             | 40.25                 |
| 2020 | 28.72                 | 32.73             | 38.55                 |

---------------------

**Question 8:**
Which age_band and demographic values contribute the most to Retail sales?

**Query:**
```sql
SELECT age_band, demographic, SUM(sales) AS total_sales
FROM clean_weekly_sales
WHERE platform ='Retail'
GROUP BY age_band, demographic
ORDER BY total_sales DESC;
```

**Results:**

| age_band     | demographic | retail_sales | percentage |
| ------------ | ----------- | ------------ | ---------- |
| unknown      | unknown     | 16067285533  | 40.52      |
| Retirees     | Families    | 6634686916   | 16.73      |
| Retirees     | Couples     | 6370580014   | 16.07      |
| Middle Aged  | Families    | 4354091554   | 10.98      |
| Young Adults | Couples     | 2602922797   | 6.56       |
| Middle Aged  | Couples     | 1854160330   | 4.68       |
| Young Adults | Families    | 1770889293   | 4.47       |

**Answer:**
Large amount of sales on retail platform is from an unknown group

----------------------------

**Question 9:**
Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

**Query:**

```sql
SELECT * FROM clean_weekly_sales;
SELECT platform, calendar_year, ROUND(AVG(avg_transaction),1) AS avg_transaction_row, ROUND((SUM(sales)::numeric/SUM(transactions)::numeric),1) AS avg_transaction_group
FROM clean_weekly_sales
GROUP BY platform, calendar_year
ORDER BY calendar_year, platform;
```

In the query, I used two ways to compute the average transaction size for each year for Retail vs Shopify. 
`avg_transaction_row` is the average of average transaction of each row (each week_date) in the data set, grouped by `platform` and `calendar_year`, while `avg_transaction_group` is calculated by dividing the `sum` of `sales` column by the `sum` of `transaction` column grouped by `platform` and `calendar_year`.
The rerults are different.

**Results:**

| calendar_year | platform | avg_transaction_row | avg_transaction_group |
| ------------- | -------- | ------------------- | --------------------- |
| 2018          | Retail   | 42.91               | 36.56                 |
| 2018          | Shopify  | 188.28              | 192.48                |
| 2019          | Retail   | 41.97               | 36.83                 |
| 2019          | Shopify  | 177.56              | 183.36                |
| 2020          | Shopify  | 174.87              | 179.03                |
| 2020          | Retail   | 40.64               | 36.56                 |

 **Answer:**
It is more accurate to find the yearly average transaction size by dividing the total of yearly sales by the total yearly amount of transactions which is `avg_transaction_group` in the above query.
If we use `avg_transaction` column to find the average, we are calculating the average of weekly average transaction size for each year. This will lead to incorrect results.

-------------------------------
