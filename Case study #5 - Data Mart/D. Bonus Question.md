**4. Bonus Question**
----------------

Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?

* region
* platform
* age_band
* demographic
* customer_type
------------------------


**Region**

**Query:**
```sql
WITH sales_cte AS
(
SELECT region,
     SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
	 SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date- interval '12 week') AND ('2020-06-15'::date + interval '11 week')
GROUP BY region
ORDER BY region
)
SELECT region, before_sales, after_sales, (after_sales - before_sales) AS difference, ROUND((after_sales - before_sales)::numeric/before_sales*100,1) AS diff_percent
FROM sales_cte
ORDER BY diff_percent;
```

**Results:**

| region        | before_sales       | after_sales | difference | diff_percent |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| ASIA          | 1637244466         | 1583807621        | -53436845       | -3.3                  |
| OCEANIA       | 2354116790         | 2282795690        | -71321100       | -3.0                  |
| SOUTH AMERICA | 213036207          | 208452033         | -4584174        | -2.2                  |
| CANADA        | 426438454          | 418264441         | -8174013        | -1.9                  |
| USA           | 677013558          | 666198715         | -10814843       | -1.6                  |
| AFRICA        | 1709537105         | 1700390294        | -9146811        | -0.5                  |
| EUROPE        | 108886567          | 114038959         | 5152392         | 4.7                   |

----------------------------
