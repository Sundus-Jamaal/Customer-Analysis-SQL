# Customer Transaction Analysis

This project contains SQL queries to analyze customer transaction and spending data, focusing on business insights such as customer segmentation, spending patterns, and product preferences.

## Project Overview

The dataset consists of three tables: 
- `customers`: Information about each customer.
- `transactions`: Details about each transaction, including transaction date and amount spent.
- `products`: Information about the products associated with each transaction.

## SQL Queries

### 1. Join Customers and Transactions
- **Purpose**: To view customer transaction data, such as the amount spent and date of transaction.
- **Insight**: Provides raw transaction data for each customer.

```sql
SELECT c.customer_id, c.customer_name, t.transaction_id, t.transaction_date, t.amount_spent
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id;
```

2. Join Customers, Transactions, and Products
Purpose: To analyze which products are purchased by which customers.
Insight: Helps identify customer preferences and product popularity.

```sql
SELECT c.customer_id, c.customer_name, t.transaction_id, t.transaction_date, p.product_name, t.amount_spent
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
JOIN products p ON t.product_id = p.product_id;
```

3. Total Spending by Each Customer
Purpose: To compute the total amount spent by each customer.
Insight: Identifies top-spending customers.

```sql
SELECT c.customer_id, c.customer_name, SUM(t.amount_spent) AS total_spent
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC;
```

4. Customer Segmentation Based on Total Spending
Purpose: To categorize customers into spending groups.
Insight: Helps segment customers for targeted marketing or retention strategies.

```sql
SELECT customer_id, customer_name, total_spent,
CASE
    WHEN total_spent > 1000 THEN 'High-Spender'
    WHEN total_spent BETWEEN 500 AND 1000 THEN 'Mid-Spender'
    ELSE 'Low-Spender'
END AS customer_segment
FROM (
    SELECT c.customer_id, c.customer_name, SUM(t.amount_spent) AS total_spent
    FROM customers c
    JOIN transactions t ON c.customer_id = t.customer_id
    GROUP BY c.customer_id, c.customer_name
) AS customer_totals;
```

5. Cumulative Spending Over Time Using Window Functions
Purpose: To compute cumulative spending for each customer over time.
Insight: Shows customer spending patterns over time.

```sql
SELECT c.customer_id, c.customer_name, t.transaction_date, t.amount_spent,
SUM(t.amount_spent) OVER (PARTITION BY c.customer_id ORDER BY t.transaction_date) AS cumulative_spent
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
ORDER BY c.customer_id, t.transaction_date;
```

6. Monthly Spending Trends
Purpose: To analyze monthly spending trends for each customer.
Insight: Helps identify seasonal trends and spending behaviors.

```sql
SELECT c.customer_id, c.customer_name, 
    DATE_FORMAT(t.transaction_date, '%Y-%m') AS month,
    SUM(t.amount_spent) AS monthly_spending
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.customer_name, month
ORDER BY month;
```

7. Average Transaction Amount by Product
Purpose: To find the average transaction amount for each product.
Insight: Identifies which products generate the highest average revenue.

```sql
SELECT p.product_name, AVG(t.amount_spent) AS avg_transaction_amount
FROM products p
JOIN transactions t ON p.product_id = t.product_id
GROUP BY p.product_name
ORDER BY avg_transaction_amount DESC;
```

8. Identify Customers with No Recent Activity
Purpose: To find customers who have not made a purchase in the last 6 months.
Insight: Helps target inactive customers for re-engagement strategies.

```sql
SELECT c.customer_id, c.customer_name
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id 
AND t.transaction_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
WHERE t.transaction_id IS NULL;
```

The SQL queries included in this project demonstrate various advanced techniques such as:

- **Joins**: Used to combine data from multiple tables to get comprehensive customer transaction insights.
- **Aggregations**: Employed to summarize transaction data, calculating totals and averages for deeper insights into spending habits.
- **Window Functions**: Used to calculate cumulative spending over time, allowing for analysis of customer loyalty and purchasing trends.
- **Subqueries**: Utilized for identifying key metrics such as top-spending customers and product performance.
- **Customer Segmentation**: Techniques to categorize customers based on spending patterns for targeted marketing strategies.

## How to Use

You can run these SQL queries in any database management system that supports SQL. Just ensure you have the necessary tables and data uploaded, including `customers`, `transactions`, and `products`.

## Contact

Feel free to connect with me for any inquiries or collaboration opportunities.
