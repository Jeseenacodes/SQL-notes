
# **SQL Percentage Calculations**

### **1. Basic Percentage**

```sql
(part / whole) * 100

# Calculate what % of customers purchased an item.
SELECT 
    COUNT(*) * 100.0 / (SELECT COUNT(*) FROM customers) AS purchase_percentage
FROM sales;

```

### **2. Percentage of Each Category**

```sql
COUNT(*) * 100.0 / SUM(COUNT(*)) OVER()

# What percentage of purchases come from each gender?
SELECT 
    gender,
    COUNT(*) AS total_purchases,
    COUNT(*) * 100.0 / SUM(COUNT(*)) OVER() AS percentage
FROM sales
GROUP BY gender;
```

### **3. Percentage Within a Group**

```sql
SUM(CASE WHEN condition THEN 1 END) * 100.0 / COUNT(*)

# What % of purchases within each category use a discount?
SELECT 
    category,
    SUM(CASE WHEN discount_applied = 'Yes' THEN 1 ELSE 0 END) * 100.0 
        / COUNT(*) AS discount_percentage
FROM sales
GROUP BY category;
```

### **4. Window-Based Percentage of Total**

```sql
value * 100.0 / SUM(value) OVER()

# Percent contribution of each row to total revenue:
SELECT
    item_purchased,
    purchase_amount,
    purchase_amount * 100.0 / SUM(purchase_amount) OVER() AS percent_of_total
FROM sales;
```

### **5. Percentage Change between 2 values**

```sql
((new_value - old_value) * 100.0 / old_value)

# What is the percentage change in sales from 2023 to 2024 for each product?
SELECT
    product,
    ((sales_2024 - sales_2023) * 100.0 / sales_2023) AS percent_change
FROM yearly_sales;
```

Using `LAG()`:

```sql
(sales - LAG(sales) OVER (ORDER BY month)) * 100.0 
/ NULLIF(LAG(sales) OVER (ORDER BY month), 0)
```

### **6. Cumulative Percentage**

```sql
SUM(value) OVER (ORDER BY value DESC) * 100.0 
    / SUM(value) OVER()

# What is the cumulative percent of total sales contributed by each product?
SELECT 
    product,
    sales,
    SUM(sales) OVER (ORDER BY sales DESC) * 100.0 
        / SUM(sales) OVER() AS cumulative_pct
FROM sales_data
ORDER BY sales DESC;
```

### **7. Percentage Within Subgroups**

```sql
COUNT(*) * 100.0 
    / SUM(COUNT(*)) OVER (PARTITION BY group_column)

# % of customers by gender per location
SELECT 
    location,
    gender,
    COUNT(*) AS cnt,
    COUNT(*) * 100.0 OVER (PARTITION BY location) AS pct_within_location
FROM customers
GROUP BY location, gender;
```

### **8. Percentage of Condition (Boolean %)**

```sql
SUM(CASE WHEN condition THEN 1 END) * 100.0 / COUNT(*)

# % of orders that used discount
SELECT 
    (SUM(CASE WHEN discount_applied = 'Yes' THEN 1 ELSE 0 END) * 100.0
     / COUNT(*)) AS discount_usage_pct
FROM orders;
```

### **9. Percentage of NULL / NOT NULL**

```sql
SUM(CASE WHEN col IS NULL THEN 1 END) * 100.0 / COUNT(*)

# What percentage of email values in the customers table are NULL and what percentage are NOT NULL?
SELECT
    SUM(CASE WHEN email IS NULL THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS null_pct,
    SUM(CASE WHEN email IS NOT NULL THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS non_null_pct
FROM customers;
```

---

### Patterns 

| Type        | Formula                                           |
| ----------- | ------------------------------------------------- |
| % of total  | `value * 100 / SUM(value) OVER()`                 |
| % per group | `value * 100 / SUM(value) OVER(PARTITION BY col)` |
| Basic %     | `(part / whole) * 100`                            |
| % change    | `((new - old) / old) * 100`                       |
| Condition % | `SUM(CASE WHEN cond THEN 1 END) * 100 / COUNT(*)` |
| Avoid zero  | `NULLIF(whole, 0)`                                |

---
