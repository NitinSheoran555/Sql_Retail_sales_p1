# Retail Sales Analysis — SQL Project

## Project Overview

**Project Title:** Retail Sales Analysis
**Level:** Beginner
**Database:** `sql_project_p1`

This project showcases core SQL skills applied to a real-world retail sales dataset. It walks through the full data analyst workflow — from building the database and cleaning raw data, to running exploratory queries and answering key business questions. It's a great starting point for anyone looking to strengthen their SQL fundamentals through hands-on practice.

---

## Objectives

- **Database Setup:** Design and build a structured retail sales table to store transactional data.
- **Data Cleaning:** Detect and remove incomplete records to ensure reliable analysis.
- **Exploratory Data Analysis (EDA):** Get familiar with the dataset through summary queries.
- **Business Problem Solving:** Write targeted SQL queries to uncover actionable insights from the data.

---

## Project Structure

### 1. Database Setup

A database named `sql_project_p1` is created, along with a `retail_sales` table that holds all transactional records including sale details, customer demographics, and financial figures.

```sql
CREATE DATABASE sql_project_p1;

USE sql_project_p1;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date       DATE,
    sale_time       TIME,
    customer_id     INT,
    gender          VARCHAR(15),
    age             INT,
    category        VARCHAR(15),
    quantity        INT,
    price_per_unit  FLOAT,
    cogs            FLOAT,
    total_sale      FLOAT
);
```

---

### 2. Data Cleaning & Exploration

Before diving into analysis, the data is inspected for quality issues. Rows with NULL values in critical columns are identified and removed to maintain data integrity.

```sql
-- Check for nulls
SELECT * FROM retail_sales
WHERE
    transactions_id IS NULL OR sale_date IS NULL OR sale_time IS NULL OR
    customer_id IS NULL OR gender IS NULL OR age IS NULL OR
    category IS NULL OR quantity IS NULL OR price_per_unit IS NULL OR
    cogs IS NULL OR total_sale IS NULL;

-- Explore the dataset
SELECT COUNT(*) AS total_records FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;
```

---

### 3. Business Questions & SQL Solutions

The following queries address specific business problems using the retail sales data:

**Q1. Fetch all sales records from a specific date (2022-11-05):**
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Q2. Find Clothing transactions with quantity 4 or more in November 2022:**
```sql
SELECT *
FROM retail_sales
WHERE
    category = 'Clothing'
    AND sale_date >= '2022-11-01'
    AND sale_date < '2022-12-01'
    AND quantity >= 4;
```

**Q3. Calculate net revenue and total orders for each product category:**
```sql
SELECT
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*)        AS total_orders
FROM retail_sales
GROUP BY category;
```

**Q4. Find the average age of customers who shopped in the Beauty category:**
```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Q5. List all transactions with a sale value above 1000:**
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Q6. Count transactions broken down by gender within each category:**
```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Q7. Identify the best-performing month by average sales in each year:**
```sql
SELECT year, month, avg_sale
FROM (
    SELECT
        YEAR(sale_date)                                                        AS year,
        MONTH(sale_date)                                                       AS month,
        ROUND(AVG(total_sale), 2)                                              AS avg_sale,
        RANK() OVER (PARTITION BY YEAR(sale_date) ORDER BY AVG(total_sale) DESC) AS rnk
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) AS ranked
WHERE rnk = 1;
```

**Q8. Get the top 5 highest-spending customers:**
```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Q9. Count distinct customers per product category:**
```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

**Q10. Segment orders into Morning, Afternoon, and Evening shifts:**
```sql
WITH shift_data AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT
    shift,
    COUNT(*) AS total_orders
FROM shift_data
GROUP BY shift;
```

---

## Key Findings

- **Diverse Customer Base:** Shoppers span a wide age range and engage across multiple product categories.
- **Premium Purchases:** A notable number of transactions exceed a total sale value of 1000, pointing to high-value buying behavior.
- **Seasonal Patterns:** Monthly averages reveal clear peaks, suggesting seasonal demand trends worth targeting.
- **Top Customers:** A small segment of customers drives a disproportionately large share of total revenue.

---

## Reports Generated

- **Category Performance Report:** Breakdown of revenue and order volume per product category.
- **Shift-wise Order Report:** Distribution of orders across Morning, Afternoon, and Evening periods.
- **Customer Value Report:** Rankings of top customers and unique buyer counts per category.

---

## Conclusion

This project covers the end-to-end SQL workflow a data analyst would follow — setting up the data infrastructure, ensuring data quality, exploring the dataset, and extracting business insights. The queries developed here can directly inform decisions around inventory planning, customer targeting, and promotional timing.

---

## Author

This project is part of my personal data analytics portfolio. It reflects my ability to work with structured data using SQL — from schema design to business insight generation. 
