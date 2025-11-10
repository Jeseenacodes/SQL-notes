

## **1. Basic SQL Commands**

| **Keyword** | **Meaning / Use**                         | **Example**                          |
| ----------- | ----------------------------------------- | ------------------------------------ |
| `SELECT`    | Retrieves data from a table               | `SELECT * FROM movies;`              |
| `FROM`      | Specifies the table to query              | `SELECT title FROM movies;`          |
| `WHERE`     | Filters rows based on condition           | `WHERE release_year < 2006;`         |
| `ORDER BY`  | Sorts the results ascending or descending | `ORDER BY rating DESC;`              |
| `DISTINCT`  | Removes duplicate rows from results       | `SELECT DISTINCT genre FROM movies;` |
| `AS`        | Renames a column or table with an alias   | `SELECT AVG(rating) AS avg_rating;`  |



## **2. Aggregate & Numeric Functions**

| **Function / Keyword** | **Purpose**                                | **Example**             |
| ---------------------- | ------------------------------------------ | ----------------------- |
| `COUNT()`              | Returns number of rows                     | `COUNT(*)`              |
| `SUM()`                | Returns total sum of numeric values        | `SUM(amount)`           |
| `AVG()`                | Calculates average of numeric values       | `AVG(rating)`           |
| `MIN()` / `MAX()`      | Returns minimum / maximum value            | `MAX(rental_rate)`      |
| `ROUND()`              | Rounds numbers to a set number of decimals | `ROUND(AVG(rating), 2)` |
| `UPPER()` / `LOWER()`  | Converts text to upper or lower case       | `LOWER(title)`          |



## **3. Data Filtering & Conditions**

| **Operator / Clause**     | **Description**             | **Example**                                      |
| ------------------------- | --------------------------- | ------------------------------------------------ |
| `AND`, `OR`, `NOT`        | Combine multiple conditions | `WHERE genre = 'Comedy' AND release_year > 2000` |
| `IN`                      | Filters by a list of values | `WHERE genre IN ('Drama', 'Action')`             |
| `BETWEEN`                 | Filters by range of values  | `WHERE release_year BETWEEN 2000 AND 2005`       |
| `LIKE`                    | Filters by text pattern     | `WHERE title LIKE 'The %'`                       |
| `IS NULL` / `IS NOT NULL` | Checks for null values      | `WHERE rating IS NOT NULL`                       |



## **4. Joins & Relationships**

| **Join Type** | **Purpose**                                           | **Example**                                                             |
| ------------- | ----------------------------------------------------- | ----------------------------------------------------------------------- |
| `INNER JOIN`  | Returns only matching rows from both tables           | `FROM movies m INNER JOIN renting r ON m.movie_id = r.movie_id`         |
| `LEFT JOIN`   | Returns all rows from left table + matches from right | `FROM renting r LEFT JOIN customers c ON r.customer_id = c.customer_id` |
| `RIGHT JOIN`  | Returns all rows from right table + matches from left | *(less common, not used in SQLite)*                                     |
| `FULL JOIN`   | Returns all rows from both tables                     | *(not always supported)*                                                |
| `SELF JOIN`   | Joins a table to itself using alias                   | `FROM employees e1 JOIN employees e2 ON e1.manager_id = e2.emp_id`      |



## **5. Grouping & Aggregation**

| **Keyword / Function** | **Purpose**                                 | **Example**                                            |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------ |
| `GROUP BY`             | Aggregates rows into groups                 | `GROUP BY genre`                                       |
| `HAVING`               | Filters grouped results (after aggregation) | `HAVING AVG(rating) > 4`                               |
| `ROLLUP`               | Adds subtotals and grand totals             | `GROUP BY ROLLUP (country, gender)`                    |
| `CUBE`                 | Adds all combinations (pivot-like summary)  | `GROUP BY CUBE (genre, release_year)`                  |
| `GROUPING SETS`        | Specifies custom combinations to aggregate  | `GROUP BY GROUPING SETS ((gender), (nationality), ())` |



## **6. Subqueries**

| **Type**                | **Purpose**                  | **Example**                                                                                 |
| ----------------------- | ---------------------------- | ------------------------------------------------------------------------------------------- |
| **Simple Subquery**     | Query inside another query   | `WHERE movie_id IN (SELECT movie_id FROM renting)`                                          |
| **Correlated Subquery** | Depends on outer query value | `WHERE rating > (SELECT AVG(rating) FROM renting r2 WHERE r2.customer_id = r1.customer_id)` |
| **Scalar Subquery**     | Returns a single value       | `SELECT (SELECT COUNT(*) FROM renting)`                                                     |
| **IN / EXISTS**         | Checks for existence of rows | `WHERE EXISTS (SELECT 1 FROM renting r WHERE r.movie_id = m.movie_id)`                      |



## **7. Set Operations**

| **Operation**      | **Purpose**                                       | **Example**                                                         |
| ------------------ | ------------------------------------------------- | ------------------------------------------------------------------- |
| `UNION`            | Combines results from two queries (no duplicates) | `SELECT country FROM customers UNION SELECT country FROM suppliers` |
| `UNION ALL`        | Combines results with duplicates                  | `SELECT * FROM a UNION ALL SELECT * FROM b`                         |
| `INTERSECT`        | Returns rows common to both queries               | `SELECT genre FROM a INTERSECT SELECT genre FROM b`                 |
| `EXCEPT` / `MINUS` | Returns rows in first query not in second         | `SELECT country FROM customers EXCEPT SELECT country FROM renting`  |



## **8. Advanced Aggregation (OLAP Extensions)**

| **Operator**      | **Purpose**                                      | **Example**                                        |
| ----------------- | ------------------------------------------------ | -------------------------------------------------- |
| `ROLLUP()`        | Aggregates hierarchical data (subtotals + total) | `GROUP BY ROLLUP (country, gender)`                |
| `CUBE()`          | Aggregates all possible combinations             | `GROUP BY CUBE (genre, release_year)`              |
| `GROUPING SETS()` | Custom aggregation levels                        | `GROUP BY GROUPING SETS ((country), (gender), ())` |



## **9. Other Useful Clauses**

| **Keyword**  | **Purpose**                            | **Example**                                       |
| ------------ | -------------------------------------- | ------------------------------------------------- |
| `LIMIT`      | Restricts number of returned rows      | `LIMIT 10`                                        |
| `OFFSET`     | Skips specified number of rows         | `LIMIT 10 OFFSET 5`                               |
| `CASE WHEN`  | Conditional logic in SQL               | `CASE WHEN rating > 4 THEN 'High' ELSE 'Low' END` |
| `ALIAS (AS)` | Rename tables/columns temporarily      | `FROM renting AS r`                               |
| `JOIN ON`    | Specifies condition for joining tables | `ON r.movie_id = m.movie_id`                      |



## **10. Typical Analysis Goals (Ch1–Ch4)**

| **Analysis Goal**                       | **Core SQL Concepts Used**                |
| --------------------------------------- | ----------------------------------------- |
| Movie promotions & price adjustments    | `LOWER()`, arithmetic, `WHERE`            |
| Top rented / highly rated movies        | `GROUP BY`, `HAVING`, `ORDER BY`          |
| Customer segmentation by gender/country | `GROUP BY`, `ROLLUP`, `CUBE`              |
| Genre performance by country            | `LEFT JOIN`, `AVG()`, `COUNT()`, `CUBE`   |
| Actor nationality diversity             | `GROUPING SETS`, `COUNT()`                |
| Genre preferences                       | `LEFT JOIN`, `HAVING`, `AVG()`, `COUNT()` |

---

