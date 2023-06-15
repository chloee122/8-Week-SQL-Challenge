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
