**1. Data Exploration and Cleansing**
--------------

**Question 1:**
Update the `fresh_segments.interest_metrics` table by modifying the `month_year` column to be a date data type with the start of the month

**Query:**
```sql
ALTER TABLE interest_metrics
ALTER COLUMN month_year TYPE DATE USING month_year:: DATE;
```
------------

**Question 2:**
What is count of records in the `fresh_segments.interest_metrics` for each `month_year` value sorted in chronological order (earliest to latest) with the null values appearing first?

**Query:**
```sql
SELECT month_year, COUNT(*)
FROM interest_metrics
GROUP BY month_year
ORDER BY month_year DESC NULLS FIRST;
```

**Results:**
|month_year|count|
|:----|:----|
|null|1194|
|2019-08-01|1149|
|2019-07-01|864|
|2019-06-01|824|
|2019-05-01|857|
|2019-04-01|1099|
|2019-03-01|1136|
|2019-02-01|1121|
|2019-01-01|973|
|2018-12-01|995|
|2018-11-01|928|
|2018-10-01|857|
|2018-09-01|780|
|2018-08-01|767|
|2018-07-01|729|


-----------------
**Question 3:**
What do you think we should do with these null values in the fresh_segments.interest_metrics

**Answers:**
Null values are not only in the `month_year` column but also in the `interest_id` column, the primary key to join the `interest_metrics` table with the `interest_map` table. This means we cannot join these values with corresponding values in the `interest_map` table to make sense of them. We can remove them as they can not be used for any other analytical purposes.

--------------
**Question 4:**
How many interest_id values exist in the fresh_segments.interest_metrics table but not in the fresh_segments.interest_map table? What about the other way around?

**Query:**
```sql
WITH join_cte AS
(
SELECT *
FROM interest_metrics me
LEFT JOIN interest_map ma ON ma.id = me.interest_id::INT
WHERE interest_id IS NOT NULL
)
SELECT interest_id
FROM join_cte
WHERE id IS NULL;
```

**Results:**
|count|
|-----|
|0    |

There isn't any interest_id value exists in the interest_metrics but not in interest_map

**Query:**
```sql
WITH join_cte AS
(
SELECT *
FROM interest_metrics me
RIGHT JOIN interest_map ma ON ma.id = me.interest_id::INT
WHERE interest_id IS NOT NULL
)
SELECT id
FROM join_cte
WHERE interest_id IS NULL;
```
**Results:**
|count|
|-----|
|7    |

There are 7 interest_id values exist in the interest_map but not in interest_metrics

-------------------

**Question 5:**
Summarise the id values in the fresh_segments.interest_map by its total record count in this table

**Query:**
```sql
SELECT COUNT(id)
FROM interest_map;
```

**Results:**
|count|
|-----|
|1209 |

**Answers:**
There are 1209 records

---------------
**Question 6:**
What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where interest_id = 21246 in your joined output and include all columns from fresh_segments.interest_metrics and all columns from fresh_segments.interest_map except from the id column.

There are `id` values in `interest_map` table that don't exist in `interest_metrics` table. Therefore, I used LEFT JOIN to keep all the values in `interest_map` in case we want to analyze those values. To check the rows in the joined table where interest_id = 21246:

**Query:**
```sql
SELECT _month, _year, month_year, interest_id, composition, index_value, ranking, percentile_ranking, interest_name, interest_summary, created_at, last_modified
FROM interest_map ma
LEFT JOIN interest_metrics me ON me.interest_id::INT = ma.id
WHERE interest_id::INT = 21246
AND month_year IS NOT NULL;
```
There is a row with `interest_id` = 21246 having null values in month_year, _month, _year so I want to filter this out.

**Results:**

|_month|_year|month_year|interest_id|composition|index_value|ranking|percentile_ranking|interest_name|interest_summary|created_at| last_modified|
|:----|:----|:----|:----|:----|:----|:----|:----|:----|:----|:----|:----|
|7|2018|2018-07-01|21246|2.26|0.65|722|0.96|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|8|2018|2018-08-01|21246|2.13|0.59|765|0.26|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|9|2018|2018-09-01|21246|2.06|0.61|774|0.77|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|10|2018|2018-10-01|21246|1.74|0.58|855|0.23|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|11|2018|2018-11-01|21246|2.25|0.78|908|2.16|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|12|2018|2018-12-01|21246|1.97|0.7|983|1.21|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|1|2019|2019-01-01|21246|2.05|0.76|954|1.95|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|2|2019|2019-02-01|21246|1.84|0.68|1109|1.07|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|3|2019|2019-03-01|21246|1.75|0.67|1123|1.14|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|
|4|2019|2019-04-01|21246|1.58|0.63|1092|0.64|Readers of El Salvadoran Content|People reading news from El Salvadoran media sources.|2018-06-11 17:50:04|2018-06-11 17:50:04|

--------------------
**Question 7:**
Are there any records in your joined table where the month_year value is before the created_at value from the fresh_segments.interest_map table? Do you think these values are valid and why?

Let's run a query to see if there are any records where the `month_year` value is before the `created_at` value
**Query:**
```sql
SELECT COUNT(*) 
FROM interest_map ma
LEFT JOIN interest_metrics me ON me.interest_id::INT = ma.id
WHERE month_year < created_at;
```

**Results:**
| count |
| ----- |
| 188   |

There are 188 rows that meet the condition.
I think these values are valid as we know that every `month_year` value was set to start at the beginning of a month. This means that the exact month_value can be at the same time or later than the created_at values too. As long as these corresponding values are on the same month, they are valid. We can check to see if they are in the same month.

**Query**
```sql
FROM interest_map ma
LEFT JOIN interest_metrics me ON me.interest_id::INT = ma.id
```

**Results:**
| count |
| ----- |
| 188   |

So, all `month_year` values and their corresponding `created_at` values are in the same months.

-------------




