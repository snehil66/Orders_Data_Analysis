# Orders_Data_Analysis

### Detailed Report: Orders Data Analysis Project

#### Introduction

This report presents a comprehensive analysis of order data extracted from Kaggle. The analysis involved the following steps:
1. **Project Architecture Creation**: Structuring the project for efficient data handling and analysis.
2. **Data Extraction**: Downloading the data from Kaggle API using pandas.
3. **ETL Process**: Cleaning and transforming the data for analysis.
4. **Database Integration**: Connecting to MS SQL Server and loading the transformed data.
5. **Detailed Analysis**: Performing various SQL queries to derive actionable insights.
6. **Reporting**: Compiling the findings into a detailed report.

#### Data Extraction and ETL Process

Using the Kaggle API and pandas, the data was extracted and cleaned. The ETL process included data validation, handling missing values, and formatting dates and numerical values for consistency. The transformed data was then loaded into MS SQL Server for analysis.

#### SQL Analysis Queries and Findings

##### 1. Top 10 Highest Revenue Generating Products
```sql
SELECT TOP 10 product_id, SUM(selling_price) AS sales
FROM df_orders
GROUP BY product_id
ORDER BY sales DESC;
```
**Findings**: The top 10 products based on revenue were identified, highlighting the most profitable items in the inventory.
![1](https://github.com/teapositve/Orders_Data_Analysis/assets/63927953/0d4a879e-80ad-40c0-9cb4-e0337c9dc582)

##### 2. Top 5 Highest Selling Products in Each Region
```sql
WITH cte AS (
    SELECT region, product_id, SUM(selling_price) AS sales
    FROM df_orders
    GROUP BY region, product_id
)
SELECT * FROM (
    SELECT *, ROW_NUMBER() OVER(PARTITION BY region ORDER BY sales DESC) AS rn
    FROM cte
) a
WHERE rn <= 5;
```
**Findings**: This query provided insights into regional preferences by identifying the top 5 products in each region.

##### 3. Month-over-Month Growth Comparison for 2022 and 2023 Sales
```sql
WITH cte AS (
    SELECT YEAR(order_date) AS order_year, MONTH(order_date) AS order_month, SUM(selling_price) AS sales
    FROM df_orders
    GROUP BY YEAR(order_date), MONTH(order_date)
)
SELECT 
    order_month, 
    SUM(CASE WHEN order_year = 2022 THEN sales ELSE 0 END) AS sales_2022,
    SUM(CASE WHEN order_year = 2023 THEN sales ELSE 0 END) AS sales_2023
FROM cte
GROUP BY order_month
ORDER BY order_month;
```
**Findings**: This analysis compared sales growth month-over-month between 2022 and 2023, revealing trends and identifying peak sales periods.

##### 4. Highest Sales Month for Each Category
```sql
WITH cte AS (
    SELECT category, FORMAT(order_date, 'yyyy-MM') AS order_year_month, SUM(selling_price) AS sales
    FROM df_orders
    GROUP BY category, FORMAT(order_date, 'yyyy-MM')
)
SELECT * FROM (
    SELECT *, ROW_NUMBER() OVER(PARTITION BY category ORDER BY sales DESC) AS rn 
    FROM cte
) a
WHERE rn = 1;
```
**Findings**: This query identified the month with the highest sales for each product category, providing insights into seasonal demand.

##### 5. Sub-Category with Highest Growth by Profit in 2023 Compared to 2022
```sql
WITH cte AS (
    SELECT sub_category, YEAR(order_date) AS order_year, SUM(selling_price) AS sales
    FROM df_orders
    GROUP BY sub_category, YEAR(order_date)
),
cte2 AS (
    SELECT 
        sub_category, 
        SUM(CASE WHEN order_year = 2022 THEN sales ELSE 0 END) AS sales_2022,
        SUM(CASE WHEN order_year = 2023 THEN sales ELSE 0 END) AS sales_2023
    FROM cte
    GROUP BY sub_category
)
SELECT TOP 1 *, (sales_2023 - sales_2022) AS Growth
FROM cte2
ORDER BY (sales_2023 - sales_2022) DESC;
```
**Findings**: The sub-category with the highest profit growth in 2023 compared to 2022 was identified, highlighting key areas of business expansion.

#### Conclusion

The detailed analysis provided valuable insights into sales performance, regional preferences, and growth trends. These findings can be leveraged to optimize inventory management, tailor marketing strategies, and enhance overall business performance.

#### Call to Action

I encourage stakeholders to review these insights and consider the recommended actions to drive further growth and profitability. Feedback and suggestions are welcome to refine this analysis and continue delivering valuable business intelligence.

---

By including this project in my portfolio, I aim to demonstrate my expertise in data analysis, SQL querying, and business intelligence, showcasing my ability to derive actionable insights from complex datasets.
