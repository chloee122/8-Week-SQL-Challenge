# Case Study #5: Data Mart

<img src="https://user-images.githubusercontent.com/81607668/131437982-fc087a4c-0b77-4714-907b-54e0420e7166.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
-  Question and Solution
    - [A. Data Cleansing](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20study%20%235%20-%20Data%20Mart/A.%20Data%20Cleansing.md)
    - [B. Data Exploration](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20study%20%235%20-%20Data%20Mart/B.%20Data%20Explopration.md)
    - [C. Before and After Analysis](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20study%20%235%20-%20Data%20Mart/C.%20Before%20and%20After%20Analysis.md)
    - [D. Bonus Question](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20study%20%235%20-%20Data%20Mart/D.%20Bonus%20Question.md)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-5/). 

***

## Business Task
Data Mart is an online supermarket that specialises in fresh produce.

In June 2020, large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.

Danny needs your help need to analyse and quantify the impact of this change on the sales performance for Data Mart and it’s separate business areas.

The key business question to answer are the following:
- What was the quantifiable impact of the changes introduced in June 2020?
- Which platform, region, segment and customer types were the most impacted by this change?
- What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?

## Entity Relationship Diagram

For this case study there is only a single table: `data_mart.weekly_sales`

<img width="287" alt="image" src="https://user-images.githubusercontent.com/81607668/131438278-45e6a4e8-7cf5-468a-937b-2c306a792782.png">

Here are some further details about the dataset:
- Data Mart has international operations using a multi-`region` strategy
- Data Mart has both, a retail and online `platform` in the form of a Shopify store front to serve their customers
- Customer `segment` and `customer_type` data relates to personal age and demographics information that is shared with Data Mart
- `transactions` is the count of unique purchases made through Data Mart and `sales` is the actual dollar amount of purchases
- Each record in the dataset is related to a specific aggregated slice of the underlying sales data rolled up into a `week_date` value which represents the start of the sales week.
 
10 random rows are shown in the table output below from `data_mart.weekly_sales`.

<img width="649" alt="image" src="https://user-images.githubusercontent.com/81607668/131438417-1e21efa3-9924-490f-9bff-3c28cce41a37.png">

***
