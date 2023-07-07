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


12 weeks after the change, sales dropped 3.3% in Asia and 3% in Oceania. Those areas had the highest negative impact in sales metrics performance during the period in 2020.

----------------------------

**Flatform**

**Query:**

```sql
WITH sales_cte AS
(
SELECT platform,
     SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
	 SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date- interval '12 week') AND ('2020-06-15'::date + interval '11 week')
GROUP BY platform
ORDER BY platform
)
SELECT platform, before_sales, after_sales, (after_sales - before_sales) AS difference, ROUND((after_sales - before_sales)::numeric/before_sales*100,1) AS diff_percent
FROM sales_cte;
```

**Results:**
| platform | before_sales | after_sales | difference | diff_percent  |
|----------|--------------------|-------------------|-----------------|-----------------------|
| Retail   | 6906861113         | 6738777279        | -168083834      | -2.4                  |
| Shopify  | 219412034          | 235170474         | 15758440        | 7.2                   |

During the period after the change, sales dropped 2.4% in Retail while increased 7.2% on Shopify plaftform.

-----------------------------

**Age**

**Query**

```sql
WITH sales_cte AS
(
SELECT age_band,
     SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
	 SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date- interval '12 week') AND ('2020-06-15'::date + interval '11 week')
GROUP BY age_band
ORDER BY age_band
)
SELECT age_band, before_sales, after_sales, (after_sales - before_sales) AS difference, ROUND((after_sales - before_sales)::numeric/before_sales*100,1) AS diff_percent
FROM sales_cte;
```

**Results:**
| age_band     | before_sales | after_sales | difference | diff_percent  |
|--------------|--------------------|-------------------|-----------------|-----------------------|
| unknown      | 2764354464         | 2671961443        | -92393021       | -3.1                  |
| Middle Aged  | 1164847640         | 1141853348        | -22994292       | -2.0                  |
| Retirees     | 2395264515         | 2365714994        | -29549521       | -1.3                  |
| Young Adults | 801806528          | 794417968         | -7388560        | -0.9                  |


The unknown age group had a drop of 3.1% in sales and was the age group with the highest negative impact on sales metrics performance during the period in 2020.

---------------------------

**Demographic**

**Query**
```sql
WITH sales_cte AS
(
SELECT demographic,
     SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
	 SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date- interval '12 week') AND ('2020-06-15'::date + interval '11 week')
GROUP BY demographic
ORDER BY demographic
)
SELECT demographic, before_sales, after_sales, (after_sales - before_sales) AS difference, ROUND((after_sales - before_sales)::numeric/before_sales*100,1) AS diff_percent
FROM sales_cte;
```

**Results:**

| demographic  | before_sales| after_sales | difference | diff_percent  |
|-------------|--------------------|-------------------|-----------------|-----------------------|
| unknown     | 2764354464         | 2671961443        | -92393021       | -3.3                  |
| Families    | 2328329040         | 2286009025        | -42320015       | -1.8                  |
| Couples     | 2033589643         | 2015977285        | -17612358       | -0.9                  |


The unknown demographic group had the highest negative impact on sales metrics performance with a sales drop of 3.3%.

--------------------------

**Customer type**

**Query**

```sql
WITH sales_cte AS
(
SELECT customer_type,
     SUM(CASE WHEN week_date < '2020-06-15' THEN sales ELSE 0 END) AS before_sales,
	 SUM(CASE WHEN week_date >= '2020-06-15' THEN sales ELSE 0 END) AS after_sales
FROM clean_weekly_sales
WHERE week_date BETWEEN ('2020-06-15'::date- interval '12 week') AND ('2020-06-15'::date + interval '11 week')
GROUP BY customer_type
ORDER BY customer_type
)
SELECT customer_type, before_sales, after_sales, (after_sales - before_sales) AS difference, ROUND((after_sales - before_sales)::numeric/before_sales*100,1) AS diff_percent
FROM sales_cte;
```

**Result:**

| customer_type | before_sales | after_sales | difference | diff_percent  |
|---------------|--------------------|-------------------|-----------------|-----------------------|
| Guest         | 2573436301         | 2496233635        | -77202666       | -3.0                  |
| Existing      | 3690116427         | 3606243454        | -83872973       | -2.3                  |
| New           | 862720419          | 871470664         | 8750245         | 1.0                   |

Guest customer group had the highest negative impact in sales metrics performance in 2020 with a sales drop of 3%.

**Conclusion:** Based on the analysis, sales in Asia and Oceania, retail platform, unknown age and demographic and customer groups are the areas that had the highest negative impact on sales metrics performance in 2020. However, we need more insights to conclude if the business's change to sustainable packaging was the cause. 
