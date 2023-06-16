**2. Interest Analysis**
---------------
**Question 1:**
Which interests have been present in all month_year dates in our dataset?

First, I check how many distinct `month_year` used in the dataset
**Query:**
```sql
SELECT DISTINCT month_year
FROM interest_metrics
WHERE month_year IS NOT NULL
ORDER BY month_year
```
**Results:**
|month_year|
|:----|
|2018-07-01|
|2018-08-01|
|2018-09-01|
|2018-10-01|
|2018-11-01|
|2018-12-01|
|2019-01-01|
|2019-02-01|
|2019-03-01|
|2019-04-01|
|2019-05-01|
|2019-06-01|
|2019-07-01|
|2019-08-01|

There are 14 rows which means 14 unique `month_year` values.
Next, I counted the distinct `month_year` of each `interest_name` to find interests which have been present in all month_year dates.
Qualified values need to have 14 distinct `month_year` values.

**Query:**
```sql
SELECT interest_name, COUNT(DISTINCT month_year)
FROM interest_map ma
LEFT JOIN interest_metrics me ON me.interest_id::INT = ma.id
GROUP BY interest_name
HAVING COUNT(DISTINCT month_year)=14;
```
**Results:**
There are 480 rows in the result set. Here is a partial result

| interest_name                                        |
| ---------------------------------------------------- |
| Accounting & CPA Continuing Education Researchers    |
| Affordable Hotel Bookers                             |
| Aftermarket Accessories Shoppers                     |
| Alabama Trip Planners                                |
| Alaskan Cruise Planners                              |
| Alzheimer and Dementia Researchers                   |
| Anesthesiologists                                    |
| Apartment Furniture Shoppers                         |
| Apartment Hunters                                    |
| Apple Fans                                           |
| Arizona Trip Planners                                |
| Arsenal Fans                                         |
| Arthritis Sufferers                                  |
| Asian Food Enthusiasts                               |
| Asthma Sufferers                                     |
| At-Home Gym Intenders                                |
| Atlanta Trip Planners                                |
| Audi Vehicle Shoppers                                |
| Audio Book Listeners                                 |
| Austin Trip Planners                                 |


