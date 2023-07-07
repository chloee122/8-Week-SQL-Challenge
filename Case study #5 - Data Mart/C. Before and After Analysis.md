**Before and After Analysis**
--------------------------

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the `week_date` value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.

We would include all week_date values for 2020-06-15 as the start of the period after the change and the previous week_date values would be before

Using this analysis approach - answer the following questions:

---------------

1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?

**Query:**

```sql
WITH before_after_cte AS
(
SELECT 
   SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
   SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date - interval '4 weeks') AND ('2020-06-15'::date + interval '3 weeks')
)
SELECT before_sales, after_sales, (after_sales-before_sales) AS difference, ROUND((after_sales-before_sales)::numeric/before_sales*100,2) AS diff_percent
FROM before_after_cte;
```

I used a cte and the `WHERE` clause to calculate the total sales before and after 2020-06-15. As 2020-06-15 is the start of the period after the change, I subtracted 4 weeks from the date to get the 4-week-before period, but I only added an `interval` of 3 weeks after that date to find the 4-week-after period.
In the main query, I selected before and after sales, calculated their difference and the growth/reduction rates

**Results:**

| before_sales | after_sales | difference | diff_percent |
| ------------ | ----------- | --------- | -------------- |
| 2345878357   | 2318994169  | -26884188 | -1.15          |

**Answer:**
Total sales for 4 weeks after 2020-06-15 decreased 1.15% compared to 4 week sales before that date.

-----------------------

2. What about the entire 12 weeks before and after?

**Query:**
```sql
WITH before_after_cte AS
(
SELECT 
   SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
   SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date - interval '12 weeks') AND ('2020-06-15'::date + interval '11 weeks')
)
SELECT before_sales, after_sales, (after_sales-before_sales) AS difference, ROUND((after_sales-before_sales)::numeric/before_sales*100,2) AS diff_percent
FROM before_after_cte;
```

**Results**

| before_sales | after_sales | difference | diff_percent  |
|--------------------|-------------------|-----------------|-----------------------|
| 7126273147         | 6973947753        | -152325394      | -2.14                 |

**Answer:**
Total sales for 12 weeks after 2020-06-15 decreased -2.14% compared to 12 week sales before that date.

------------------------

3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

**Query**
First, I found the week_number of this date to make it more easy in querying.
```sql
SELECT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15';
```
* week_number of '2020-06-15' is 25

* For 4 week period:
```sql
WITH sales_cte AS
(
SELECT calendar_year,
   SUM(CASE WHEN week_number < 25 THEN sales ELSE 0 END) AS before_sales,
   SUM(CASE WHEN week_number >= 25 THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_number BETWEEN 21 AND 28 
GROUP BY calendar_year
ORDER BY calendar_year
)
SELECT calendar_year,
       before_sales,
	   after_sales,
	   (after_sales-before_sales) AS difference,
	   ROUND((after_sales-before_sales)::numeric/before_sales*100,2) AS growth_reduction
FROM sales_cte;
```

**Results**
| calendar_year | before_sales | after_sales | difference | growth_reduction |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| 2018          | 2125140809         | 2129242914        | 4102105         | 0.19                  |
| 2019          | 2249989796         | 2252326390        | 2336594         | 0.10                  |
| 2020          | 2345878357         | 2318994169        | -26884188       | -1.15                 |

**Answer:**
We can see that in 2018 and 2019 sales were growing after week 25, but the year 2020 shows drop in sales after the change took place in week 25.

* For 12 week period:
```sql
WITH sales_cte AS
(
SELECT calendar_year,
   SUM(CASE WHEN week_number < 25 THEN sales ELSE 0 END) AS before_sales,
   SUM(CASE WHEN week_number >= 25 THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_number BETWEEN 13 AND 36
GROUP BY calendar_year
ORDER BY calendar_year
)
SELECT calendar_year,
       before_sales,
	   after_sales,
	   (after_sales-before_sales) AS difference,
	   ROUND((after_sales-before_sales)::numeric/before_sales*100,2) AS growth_reduction
FROM sales_cte;
```

**Results**

| calendar_year | before_sales | after_sales| difference| growth_reduction  |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| 2018          | 6396562317         | 6500818510        | 104256193       | 1.63                  |
| 2019          | 6883386397         | 6862646103        | -20740294       | -0.30                 |
| 2020          | 7126273147         | 6973947753        | -152325394      | -2.14                 |

**Answer:**
There were a sales growth in 2018, and sales reduction in 2019 and 2020. However, the reduction is larger in 2020.

-----------
