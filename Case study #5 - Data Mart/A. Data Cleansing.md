**Case study questions**
---
In a single query, perform the following operations and generate a new table in the `data_mart` schema named `clean_weekly_sales`:

1. Convert the `week_date` to a DATE format

2. Add a `week_number` as the second column for each `week_date` value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

3. Add a `month_number` with the calendar month for each `week_date` value as the 3rd column

4. Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values

5. Add a new column called `age_band` after the original `segment` column using the following mapping on the number inside the `segment` value

| segment | age_band |
|---------|----------|
| 1 | Young Adults |
| 2	| Middle Aged |
| 3 | or 4 Retirees |

6. Add a new `demographic` column using the following mapping for the first letter in the `segment` values:

| segment | demographic |
|---------|-------------|
| C	| Couples |
| F |	Families |

7. Ensure all null string values with an "unknown" string value in the original `segment` column as well as the new `age_band and `demographic` columns

8. Generate a new `avg_transaction` column as the `sales` value divided by `transactions` rounded to 2 decimal places for each record

**Query:**
```sql
SHOW datestyle;
SET datestyle to DMY;

DROP TABLE IF EXISTS clean_weekly_sales;
CREATE TABLE clean_weekly_sales AS 
(
SELECT week_date :: date as week_date,
       extract('week' from week_date::date) as week_number, 
       extract('month' from week_date::date) as month_number, 
       extract('year' from week_date::date) as calendar_year, 
       region,
       platform,
       CASE WHEN segment = 'null' THEN 'Unknown'
            ELSE segment
            END AS segment, 
       CASE WHEN segment LIKE '%1' THEN 'Young Adults'
            WHEN segment LIKE '%2' THEN 'Middle Aged'
            WHEN segment LIKE'%3' OR segment='%4'  THEN 'Retirees'
            ELSE 'Unknown'
            END AS age_band, 
       CASE WHEN segment LIKE 'C%' THEN 'Couples'
            WHEN segment LIKE 'F%' THEN 'Families'
            ELSE 'Unknown'
            END AS demographic,
       customer_type,
       transactions,
       sales,
       round((sales/transactions),2) as avg_transaction
  FROM weekly_sales
);
```
**Results:**
There are more than 17000 rows, so I only show 20 rows here
<details>
  <summary> Click here see </summary>
  
|week_date|week_number|month_number|calendar_year|region|platform|segment|age_band|demographic|customer_type|transactions|sales|avg_transactions|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|2020-08-31|36|8|2020|ASIA|Retail|C3|Retirees|Couples|New|120631|3656163|30.00|
|2020-08-31|36|8|2020|ASIA|Retail|F1|Young Adults|Families|New|31574|996575|31.00|
|2020-08-31|36|8|2020|USA|Retail|Unknown|Unknown|Unknown|Guest|529151|16509610|31.00|
|2020-08-31|36|8|2020|EUROPE|Retail|C1|Young Adults|Couples|New|4517|141942|31.00|
|2020-08-31|36|8|2020|AFRICA|Retail|C2|Middle Aged|Couples|New|58046|1758388|30.00|
|2020-08-31|36|8|2020|CANADA|Shopify|F2|Middle Aged|Families|Existing|1336|243878|182.00|
|2020-08-31|36|8|2020|AFRICA|Shopify|F3|Retirees|Families|Existing|2514|519502|206.00|
|2020-08-31|36|8|2020|ASIA|Shopify|F1|Young Adults|Families|Existing|2158|371417|172.00|
|2020-08-31|36|8|2020|AFRICA|Shopify|F2|Middle Aged|Families|New|318|49557|155.00|
|2020-08-31|36|8|2020|AFRICA|Retail|C3|Retirees|Couples|New|111032|3888162|35.00|
|2020-08-31|36|8|2020|USA|Shopify|F1|Young Adults|Families|Existing|1398|260773|186.00|
|2020-08-31|36|8|2020|OCEANIA|Shopify|C2|Middle Aged|Couples|Existing|4661|882690|189.00|
|2020-08-31|36|8|2020|SOUTH AMERICA|Retail|C2|Middle Aged|Couples|Existing|1029|38762|37.00|
|2020-08-31|36|8|2020|SOUTH AMERICA|Shopify|C4|Unknown|Couples|New|6|917|152.00|
|2020-08-31|36|8|2020|EUROPE|Shopify|F3|Retirees|Families|Existing|115|35215|306.00|
|2020-08-31|36|8|2020|OCEANIA|Retail|F3|Retirees|Families|Existing|551905|30371770|55.00|
|2020-08-31|36|8|2020|ASIA|Shopify|C3|Retirees|Couples|Existing|1969|374327|190.00|
|2020-08-31|36|8|2020|AFRICA|Retail|F1|Young Adults|Families|Existing|97604|5185233|53.00|
|2020-08-31|36|8|2020|OCEANIA|Retail|C2|Middle Aged|Couples|New|111219|2980673|26.00|
|2020-08-31|36|8|2020|USA|Retail|F1|Young Adults|Families|New|11820|463738|39.00|
</details>


