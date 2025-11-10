# Introduction to Data-Driven Decision Making module:

### **Course Overview**

* **Instructors:** Irene Ortner, Prof. Bart Baesens, and Prof. Tim Verdonck
* **Focus:** Learn how to use SQL for data-driven business insights and decision-making.

### **Course Aims**

* Refresh basic SQL skills and apply them to business contexts.
* Learn **advanced SQL techniques** such as **CUBE**, **ROLLUP**, and **GROUPING SETS** — tools for summarizing and analyzing data in business intelligence.

### **Case Study: MovieNow**

* A fictional **online movie rental platform** where customers rent movies for 24 hours.
* Data stored in several related tables:

  * **customers:** customer details (ID, name, country, gender, DOB, account creation date)
  * **movies:** movie details (ID, title, genre, runtime, release year, rental cost)
  * **renting:** rental transactions (ID, customer, movie, rating 1–10, rental date)
  * **actors:** actor details (ID, name, birth year, nationality, gender)
  * **actsin:** links movies to actors (movie_id, actor_id)

### **Objectives of Data-Driven Decision Making**

* **Short-term goals:**

  * Use data (e.g., actor popularity, last month’s revenue) for operational and investment decisions.
* **Long-term goals:**

  * Analyze trends in customer growth and regional success to guide market expansion and strategic investments.

### **Key Performance Indicators (KPIs)**

* **Revenue:** Sum of rental prices.
* **Customer Satisfaction:** Average movie rating.
* **Customer Engagement:** Number of active customers over time.
* Learn to define, extract, and monitor KPIs to support evidence-based decision-making.


### **Next Step**
Began exploring the MovieNow database to apply SQL concepts and generate actionable insights.

---

### Filtering and Ordering Data in SQL

### **Core Concept**

* Filtering retrieves only **relevant records** for a given question.
* Ordering organizes results to make analysis clearer and more meaningful.

### **Filtering with `WHERE`**

* The `WHERE` clause selects rows that meet specific conditions.

  * Example:

    ```sql
    SELECT * FROM customers
    WHERE country = 'Italy';
    ```

### **Operators Used in `WHERE`**

1. **Comparison Operators:**

   * `=`, `!=`, `>`, `<`, `>=`, `<=`
   * Example:

     ```sql
     SELECT * FROM movies
     WHERE genre != 'Drama';
     ```

2. **BETWEEN:**

   * Filters rows between two values (inclusive).

     ```sql
     SELECT * FROM customers
     WHERE account_created BETWEEN '2018-01-01' AND '2018-09-30';
     ```

3. **IN:**

   * Matches any value in a list.

     ```sql
     SELECT * FROM actors
     WHERE nationality IN ('USA', 'Australia');
     ```

4. **NULL / NOT NULL:**

   * Checks for missing data.

     ```sql
     SELECT * FROM renting
     WHERE rating IS NULL;
     ```

### **Combining Conditions**

* **AND:** All conditions must be true.

  ```sql
  WHERE country = 'Italy' AND account_created BETWEEN '2018-01-01' AND '2018-09-30';
  ```
* **OR:** At least one condition must be true.

  ```sql
  WHERE country = 'Italy' OR account_created BETWEEN '2018-01-01' AND '2018-09-30';
  ```

### **Ordering Results**

* **ORDER BY:** Sort results by one or more columns.

  ```sql
  ORDER BY rating;        -- Ascending (default)
  ORDER BY rating DESC;   -- Descending
  ```

### **Key Takeaway**

Filtering and ordering let analysts **target, compare, and prioritize** data — foundational skills for extracting actionable business insights.

---

### Aggregations – Summarizing Data

### **Core Concept**

For decision-making, we often analyze **summaries** instead of individual records — e.g., **average ratings**, **total rentals**, or **count of customers** — to understand trends and performance.

### **Aggregate Functions**

SQL provides several built-in functions to summarize data:

* **AVG()** – Calculates the average value.
* **SUM()** – Returns the total sum.
* **COUNT()** – Counts rows or non-NULL values.
* **MIN() / MAX()** – Find the smallest or largest value.

*Example:*

```sql
SELECT AVG(renting_price) AS avg_rent_price
FROM movies;
```

### **Handling NULL Values**

* `COUNT(*)` counts **all rows**.
* `COUNT(column)` counts only **non-NULL values**.
* `AVG`, `SUM`, `MIN`, and `MAX` also **ignore NULL values** in calculations.
  *Example:*

```sql
SELECT COUNT(year_of_birth) FROM actors;
-- Returns fewer results if some birth years are NULL
```

### **Using DISTINCT**

Removes duplicates to return **unique values**.
*Example:*

```sql
SELECT DISTINCT country FROM customers;
SELECT COUNT(DISTINCT country) FROM customers;
```

* `DISTINCT` includes `NULL` as one unique value.
* When sorted, `NULL` appears **last** (treated as the largest value).

### **Aliasing Columns with `AS`**

Rename output columns to make results clearer and professional.
*Example:*

```sql
SELECT AVG(price) AS avg_price, COUNT(genre) AS genre_count
FROM movies;
```

### **Key Takeaway**

Aggregation transforms raw data into **concise, meaningful summaries** — a foundation for KPI creation and data-driven insights.

---

