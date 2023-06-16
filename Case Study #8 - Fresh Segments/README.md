# Case Study #8: Fresh Segments

<img src="https://user-images.githubusercontent.com/81607668/138843936-d1741a39-9b87-4d5d-b09c-643600e28c92.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution]
   - [A. Data Exploration and Cleansing](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20Study%20%238%20-%20Fresh%20Segments/A.%20Data%20Exploration%20and%20Cleansing.md)
   - [B. Interest Analysis](https://github.com/chloee122/8-Week-SQL-Challenge/blob/main/Case%20Study%20%238%20-%20Fresh%20Segments/B.%20Interest%20Analysis.md)
   - [C. Segment Analysis]
   - [D. Index Analysis]

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-8/). 

***

## Business Task

Fresh Segments is a digital marketing agency that helps other businesses analyse trends in online ad click behaviour for their unique customer base.

Clients share their customer lists with the Fresh Segments team who then aggregate interest metrics and generate a single dataset worth of metrics for further analysis.

In particular - the composition and rankings for different interests are provided for each client showing the proportion of their customer list who interacted with online assets related to each interest for each month.

Danny has asked for your assistance to analyze aggregated metrics for an example client and provide some high-level insights about the customer list and their interests.

***

## Entity Relationship Diagram

**Table: `interest_map`**
| id | interest_name             | interest_summary                                                                   | created_at              | last_modified           |
|----|---------------------------|------------------------------------------------------------------------------------|-------------------------|-------------------------|
| 1  | Fitness Enthusiasts       | Consumers using fitness tracking apps and websites.                                | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 2  | Gamers                    | Consumers researching game reviews and cheat codes.                                | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 3  | Car Enthusiasts           | Readers of automotive news and car reviews.                                        | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 4  | Luxury Retail Researchers | Consumers researching luxury product reviews and gift ideas.                       | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 5  | Brides & Wedding Planners | People researching wedding ideas and vendors.                                      | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 6  | Vacation Planners         | Consumers reading reviews of vacation destinations and accommodations.             | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:13.000 |
| 7  | Motorcycle Enthusiasts    | Readers of motorcycle news and reviews.                                            | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:13.000 |
| 8  | Business News Readers     | Readers of online business news content.                                           | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |
| 12 | Thrift Store Shoppers     | Consumers shopping online for clothing at thrift stores and researching locations. | 2016-05-26T14:57:59.000 | 2018-03-16T13:14:00.000 |
| 13 | Advertising Professionals | People who read advertising industry news.                                         | 2016-05-26T14:57:59.000 | 2018-05-23T11:30:12.000 |

**Table: `interest_metrics`**

| month | year | month_year | interest_id | composition | index_value | ranking | percentile_ranking |
|-------|------|------------|-------------|-------------|-------------|---------|--------------------|
| 7     | 2018 | Jul-18     | 32486       | 11.89       | 6.19        | 1       | 99.86              |
| 7     | 2018 | Jul-18     | 6106        | 9.93        | 5.31        | 2       | 99.73              |
| 7     | 2018 | Jul-18     | 18923       | 10.85       | 5.29        | 3       | 99.59              |
| 7     | 2018 | Jul-18     | 6344        | 10.32       | 5.1         | 4       | 99.45              |
| 7     | 2018 | Jul-18     | 100         | 10.77       | 5.04        | 5       | 99.31              |
| 7     | 2018 | Jul-18     | 69          | 10.82       | 5.03        | 6       | 99.18              |
| 7     | 2018 | Jul-18     | 79          | 11.21       | 4.97        | 7       | 99.04              |
| 7     | 2018 | Jul-18     | 6111        | 10.71       | 4.83        | 8       | 98.9               |
| 7     | 2018 | Jul-18     | 6214        | 9.71        | 4.83        | 8       | 98.9               |
| 7     | 2018 | Jul-18     | 19422       | 10.11       | 4.81        | 10      | 98.63              |

