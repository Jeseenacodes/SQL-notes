

#  DATA EXPLORATION BEFORE CLEANING

| **Step**                    | **What to Explore**               | **Why It Matters**             | **Example SQL / Tip**                                                                  |
| --------------------------- | --------------------------------- | ------------------------------ | -------------------------------------------------------------------------------------- |
| **1. Structure Overview**   | Tables, columns, data types       | Know what data exists          | `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME='table';`                   |
| **2. Row Counts**           | Number of rows per table          | Spot empty or small tables     | `SELECT COUNT(*) FROM table;`                                                          |
| **3. Keys & Relationships** | Primary/foreign keys              | Understand joins & uniqueness  | `SELECT * FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE;`                                   |
| **4. Column Summary**       | Distinct, NULLs, min/max          | Detect missing or odd values   | `SELECT COUNT(DISTINCT col), SUM(CASE WHEN col IS NULL THEN 1 ELSE 0 END) FROM table;` |
| **5. Duplicates**           | Repeated IDs or rows              | Prevent double counting        | `SELECT col, COUNT(*) FROM table GROUP BY col HAVING COUNT(*)>1;`                      |
| **6. Data Type Issues**     | Text stored as numbers, bad dates | Prevent query errors           | `SELECT * FROM table WHERE ISNUMERIC(col)=0;`                                          |
| **7. Dates & Time**         | Range and validity                | Spot future or past errors     | `SELECT MIN(date_col), MAX(date_col) FROM table;`                                      |
| **8. Missing Values**       | Columns with many NULLs           | Plan to fill, drop, or replace | `SELECT SUM(CASE WHEN col IS NULL THEN 1 ELSE 0 END) FROM table;`                      |
| **9. Text Consistency**     | Case, spaces, spelling            | Plan standardization           | `SELECT DISTINCT col FROM table;`                                                      |
| **10. Outliers**            | Extreme or invalid values         | Catch bad entries              | `SELECT * FROM table WHERE amount < 0;`                                                |
| **11. Cross-Table Checks**  | Foreign key integrity             | Find orphan records            | `SELECT * FROM orders WHERE customer_id NOT IN (SELECT customer_id FROM customers);`   |
| **12. Business Logic**      | Logical correctness               | Detect data errors             | `SELECT * FROM sales WHERE sale_date < signup_date;`                                   |
| **13. Documentation**       | Notes of issues found             | Track cleaning plan            | `-- Found NULLs in email column`                                                       |

---
