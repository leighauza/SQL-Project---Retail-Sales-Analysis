# Retail Sales Analysis SQL Project

## Project Overview

This SQL project demonstrates data analysis skills through a retail sales database. This showcases the basic process of using SQL for analysis starting from setting up the database, cleaning data, performing exploratory analysis (EDA), and answering business questions, and reporting on overall insights.

## Objectives

1. **Database Setup**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Tools I Used

For my analysis of the retail sales database, I utilized the power of several key tools:

- SQL: The backbone of my analysis, allowing me to query the database and unearth critical insights.
- PostgreSQL: The chosen database management system, ideal for handling the retail sales data.
- Visual Studio Code: My go-to for database management and executing SQL queries.
- Git & GitHub: Essention for version control and sharing my SQL scripts and analysis, ensuring project tracking.

## Project Structure

### 1. **Database Setup**

```sql
CREATE TABLE retail_sales_db (
	transaction_id INT PRIMARY KEY,
	sale_date DATE,
	sale_time TIME,
	customer_id INT,
	gender VARCHAR(6),
	age INT,
	clothing VARCHAR(30),
	quantity INT,
	price_per_unit NUMERIC(7,2),
	cogs NUMERIC(7,2),
	total_sale NUMERIC(7,2)
);
```

### 2. **Data Cleaning**

```sql
DELETE FROM retail_sales_db
WHERE
	age IS NULL OR
	quantity IS NULL OR
	price_per_unit IS NULL OR
	cogs IS NULL OR
	total_sale IS NULL;
```

Data reviewed before deleting.
Final Count: 1987 rows

### 3. **Exploratory Data Analysis (EDA)**

**Total sales for the year 2022 vs. 2023**

```sql
SELECT
    EXTRACT(YEAR FROM sale_date) AS year,
    ROUND(SUM(total_sale), 2) AS sum_total_sale
FROM
    retail_sales_db
GROUP BY
    EXTRACT(YEAR FROM sale_date)
ORDER BY
    year;
```

| year | sum_total_sale |
|------|---------------|
| 2022 | 449,335.00    |
| 2023 | 458,895.00    |

**Average sales for the year 2022 vs. 2023**

```sql
SELECT
	EXTRACT(YEAR FROM sale_date) AS year,
	ROUND(AVG(total_sale),2) AS average_total_sale
FROM
	retail_sales_db
GROUP BY
	year
ORDER BY
	year;
```

| year | average_total_sale |
|------|------------------|
| 2022 | 465.15          |
| 2023 | 449.46          |

**Customer Count**

```sql
SELECT
  EXTRACT(YEAR FROM sale_date) AS year,
  COUNT(DISTINCT customer_id) AS customer_count
FROM
  retail_sales_db
GROUP BY
  year
ORDER BY
  year;
```

| year | customer_count |
|------|----------------|
| 2022 | 155            |
| 2023 | 147            |

### 4. **Business Analysis**

**Category Performance**

```sql
SELECT
  EXTRACT(YEAR FROM sale_date) as year,
  category,
  SUM(total_sale)
FROM
  retail_sales_db
GROUP BY
  year,
  category
ORDER BY
  year,
  category
;
```
| Year | Category    | Sum        |
|------|------------|------------|
| 2022 | Beauty     | 151,460.00 |
| 2022 | Clothing   | 148,780.00 |
| 2022 | Electronics| 149,095.00 |
| 2023 | Beauty     | 135,330.00 |
| 2023 | Clothing   | 161,215.00 |
| 2023 | Electronics| 162,350.00 |

*Overall Category Trends:*
- Beauty category saw a significant decline, dropping from $151,460 to $135,330 (-10.6%) from 2022 to 2023
- Both Clothing and Electronics showed strong growth:
    - Clothing increased from $148,780 to $161,215 (+8.4%)
    - Electronics grew from $149,095 to $162,350 (+8.9%)

*Category Distribution Shifts:*
- In 2022, sales were fairly evenly distributed across categories (all around $149-151K)
- 2023 showed more polarization, with Beauty falling behind ($135K) while Clothing and Electronics both moved ahead (both above $161K)

Examine whether average sale or frequency is contributing to changes in sale.

**Average Sale per Category**

```sql
SELECT
  EXTRACT(YEAR FROM sale_date) as year,
  category,
  ROUND(AVG(total_sale), 2)
FROM
  retail_sales_db
GROUP BY
  year,
  category
ORDER BY
  year,
  category
;
```
| Year | Category    | Average Sale |
|------|------------|--------------|
| 2022 | Beauty     | 487.01       |
| 2022 | Clothing   | 450.85       |
| 2022 | Electronics| 458.75       |
| 2023 | Beauty     | 451.10       |
| 2023 | Clothing   | 438.08       |
| 2023 | Electronics| 459.92       |

**Count per Category**

```sql
SELECT
  EXTRACT(YEAR FROM sale_date) as year,
  category,
  COUNT(transaction_id)
FROM
  retail_sales_db
GROUP BY
  year,
  category
ORDER BY
  year,
  category
;
```
| Year | Category    | Count |
|------|------------|--------|
| 2022 | Beauty     | 311    |
| 2022 | Clothing   | 330    |
| 2022 | Electronics| 325    |
| 2023 | Beauty     | 300    |
| 2023 | Clothing   | 368    |
| 2023 | Electronics| 353    |

*Transaction Count vs Average Sale Impact:*

- The changes in total sales were primarily driven by transaction volume rather than average sale value.
- Average sales decreased for both Beauty and Clothing categories, while Electronics remained stable. None of the categories showed an increase in average sale value.
- The success in Clothing and Electronics appears to be driven by increased customer frequency rather than higher transaction values
- Beauty's decline is compounded by both fewer transactions and lower average sales, suggesting potential issues with both customer retention and pricing strategy

**Count per Customer Demographic**
Explore the shift of count based on demographic

```sql
SELECT
	category,
  gender,
  COUNT(
    CASE
      WHEN EXTRACT(YEAR FROM sale_date) = '2022'
        THEN transaction_id
      ELSE NULL
    END
  ) AS total_count_2022
  ,
  COUNT(
    CASE
      WHEN EXTRACT(YEAR FROM sale_date) = '2023'
        THEN transaction_id
      ELSE NULL
    END
  ) AS total_count_2023
  ,
  COUNT(transaction_id) AS total_count_all

FROM
	retail_sales_db
GROUP BY
	category,
  gender
ORDER BY
  category,
  gender
;
```
| Category    | Gender | 2022 Count | 2023 Count | Total Count |
|------------|--------|------------|------------|-------------|
| Beauty     | Female | 171        | 159        | 330         |
| Beauty     | Male   | 140        | 141        | 281         |
| Clothing   | Female | 160        | 187        | 347         |
| Clothing   | Male   | 170        | 181        | 351         |
| Electronics| Female | 169        | 166        | 335         |
| Electronics| Male   | 156        | 187        | 343         |

*Significant gender-based shifts from 2022 to 2023:*
- Beauty maintained stable male customer count but lost female customers
- Both genders increased their Clothing purchases
- Electronics saw strong growth in male customers while female customers slightly decreased

**Top Customers and their Sale Patterns between 2022 vs 2023**

```sql
SELECT
	customer_id,
  SUM(
    CASE
      WHEN EXTRACT(YEAR FROM sale_date) = '2023'
        THEN total_sale
      ELSE 0
    END
  ) AS total_sale_2023
  ,
  SUM(
    CASE
      WHEN EXTRACT(YEAR FROM sale_date) = '2022'
        THEN total_sale
      ELSE 0
    END
   ) AS total_sale_2022,
  SUM(total_sale) AS total_sale_all

FROM
	retail_sales_db
GROUP BY
	customer_id
ORDER BY
	total_sale_all DESC
LIMIT 20
;
```
| Customer ID | Total Sale 2023 | Total Sale 2022 | Total Sale All |
|-------------|----------------|----------------|---------------|
| 3           | 35,380.00      | 3,060.00       | 38,440.00     |
| 1           | 27,180.00      | 3,570.00       | 30,750.00     |
| 5           | 28,095.00      | 2,310.00       | 30,405.00     |
| 2           | 21,525.00      | 3,770.00       | 25,295.00     |
| 4           | 23,220.00      | 360.00         | 23,580.00     |
| 87          | 9,055.00       | 6,800.00       | 15,855.00     |
| 54          | 5,675.00       | 7,800.00       | 13,475.00     |
| 71          | 5,125.00       | 7,665.00       | 12,790.00     |
| 55          | 3,900.00       | 8,180.00       | 12,080.00     |
| 84          | 1,140.00       | 10,590.00      | 11,730.00     |
| 76          | 8,485.00       | 2,750.00       | 11,235.00     |
| 52          | 1,965.00       | 8,360.00       | 10,325.00     |
| 86          | 5,400.00       | 4,685.00       | 10,085.00     |
| 91          | 7,200.00       | 2,765.00       | 9,965.00      |
| 61          | 3,610.00       | 6,250.00       | 9,860.00      |
| 111         | 1,675.00       | 8,170.00       | 9,845.00      |
| 82          | 4,610.00       | 4,750.00       | 9,360.00      |
| 65          | 2,100.00       | 7,220.00       | 9,320.00      |
| 75          | 580.00         | 8,710.00       | 9,290.00      |
| 67          | 175.00         | 9,050.00       | 9,225.00      |

*Customer Spending Patterns:*
- The top 5 customers showed dramatic increases in spending from 2022 to 2023, with Customer #3 increasing from $3,060 to $35,380.
- There's a clear divide between high-value customers (top 5) and the rest, with a significant drop in total sales after the fifth customer.

**Sales Trend per Month**

```sql
SELECT
	EXTRACT(MONTH FROM sale_date) AS month_no,
	ROUND(AVG(total_sale), 2) AS total_sale
FROM
	retail_sales_db
WHERE
	EXTRACT(YEAR FROM sale_date) = '2023' -- Same code for '2022'
GROUP BY
	EXTRACT(MONTH FROM sale_date)
ORDER BY
	month_no
;
```

| Month | Total Sale 2023|
|-------|------------|
| 1     | 396.50     |
| 2     | 535.53     |
| 3     | 394.81     |
| 4     | 466.49     |
| 5     | 450.17     |
| 6     | 438.48     |
| 7     | 427.68     |
| 8     | 495.96     |
| 9     | 462.74     |
| 10    | 399.17     |
| 11    | 453.45     |
| 12    | 490.39     |

| Month | Total Sale 2022|
|-------|------------|
| 1     | 397.11     |
| 2     | 366.14     |
| 3     | 521.22     |
| 4     | 500.61     |
| 5     | 480.00     |
| 6     | 481.40     |
| 7     | 541.34     |
| 8     | 390.28     |
| 9     | 485.20     |
| 10    | 467.14     |
| 11    | 472.02     |
| 12    | 460.77     |

*Seasonal Patterns*
- 2023 peaked in February ($535.53), showing strong post-holiday sales
- 2022's highest point was in July ($541.34), indicating strong mid-year performance
*Year-over-Year Changes*
- Average monthly sales remained relatively stable between $400-500 across both years
- 2023 showed less volatility compared to 2022, with smaller fluctuations between months
*Notable Trends*
- Both years show stronger performance in mid-year months (April-September)
- October typically sees a dip in sales before recovering for the holiday season
- December maintains consistently strong performance (2022: $460.77, 2023: $490.39)
  
**Sales Trend per Time Shift**

```sql
WITH shift_time AS(
SELECT
	transaction_id,
	sale_time,
	CASE
		WHEN sale_time < '12:00' THEN 'Morning'
		WHEN sale_time BETWEEN '12:00' AND '17:00' THEN 'Afternoon'
		ELSE 'Evening'
	END AS shift

FROM
	retail_sales_db
)

SELECT
	shift,
	COUNT(transaction_id)
FROM shift_time
GROUP BY
	shift
ORDER BY
	COUNT(transaction_id) DESC
;
```

| Shift     | Count |
|-----------|-------|
| Evening   | 1275  |
| Morning   | 548   |
| Afternoon | 164   |

*Peak Shopping Hours*
- Evening dominates with 1,275 transactions (64% of total sales), indicating strong after-work shopping preference
- Morning follows with 548 transactions (28%), suggesting moderate pre-work and weekend shopping activity
*Operational Insights*
- Afternoon shows lowest traffic (164 transactions, 8%), presenting an opportunity for targeted promotions
- The strong evening preference suggests potential for extended hours or special evening events

## Conclusion

The retail sales analysis reveals significant trends and transformations in the business performance across 2022-2023. The company demonstrated resilient growth with total sales increasing from $449,335 to $458,895. While this shows positive momentum, a slight decline in average transaction value from $465.15 to $449.46suggests changing purchasing patterns.

Category performance varied significantly. Electronics and Clothing emerged as growth drivers, with impressive gains of 8.9% and 8.4% respectively. However, the Beauty segment faced challenges with a concerning 10.6% decline, indicating a need for strategic intervention.

Customer segmentation analysis unveiled interesting dynamics. The most notable development was the exceptional growth in high-value customers, particularly exemplified by Customer #3's remarkable increase in spending from $3,060 to $35,380. Gender-specific trends revealed nuanced patterns across categories - while Clothing showed universal appeal with growth across genders, Electronics demonstrated stronger traction with male customers. The Beauty category maintained its male customer base but experienced attrition among female customers, highlighting a critical area for attention.

## Recommendations

Based on our findings, here are key recommendations to drive growth and improve customer retention:

- Investigate and address the decline in Beauty category, particularly focusing on female customer retention
- Leverage the success factors driving growth in Electronics and Clothing categories
- Develop strategies to expand the high-value customer base, given the significant gap between top 5 customers and others
