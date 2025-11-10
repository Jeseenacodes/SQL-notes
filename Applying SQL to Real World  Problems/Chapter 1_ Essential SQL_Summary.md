### Essential SQL — Summary

**Overview:**
This introductory lesson sets the foundation for working with SQL using a real-world database example, teaching the most fundamental SQL commands for data retrieval and filtering.

---

#### **Key Concepts Covered**

1. **Course Introduction**

   * Four chapters:
     1️⃣ Essential SQL commands
     2️⃣ Finding data in databases
     3️⃣ Managing data
     4️⃣ Writing clean, readable SQL scripts
   * Focus: Real-world problem-solving using the **Pagila** (DVD rental) database.

2. **Database Overview**

   * **Pagila database** mimics a DVD rental store.
   * Contains **10 tables** — covering actors, films, customers, payments, etc.
   * Used throughout the course for SQL practice.

3. **Core SQL Commands**

   * **SELECT** → Choose specific columns (or use `*` for all).
   * **FROM** → Identify the table to query.
   * **INNER JOIN** → Combine data from multiple tables using matching columns (`ON` condition).
   * **WHERE** → Filter records based on criteria.
   * **AND / IN** → Add multiple filtering conditions; `IN` simplifies multiple OR statements.
   * **ORDER BY / DESC** → Sort results; use `DESC` for descending order (e.g., longest films).

4. **Example Query:**

   ```sql
   SELECT f.title, f.length
   FROM film AS f
   INNER JOIN film_category AS fc ON f.film_id = fc.film_id
   INNER JOIN category AS c ON fc.category_id = c.category_id
   WHERE c.name = 'Documentary' AND f.rating IN ('G', 'PG')
   ORDER BY f.length DESC;
   ```

   Returns **longest family-friendly documentaries**.

** Takeaway:**
This lesson provides the essential SQL building blocks — **SELECT, FROM, JOIN, WHERE, ORDER BY** — 
that serve as the backbone for all analytical queries in real-world databases.
Here’s a concise summary of your lesson:

---

### Transforming Your Results (SQL)

**1. Overview**

* Focus: Transforming columns in SQL queries to make results more meaningful and usable.

**2. String Transformations**

* Functions:

  * `UPPER(column)` → Converts text to uppercase.
  * `LOWER(column)` → Converts text to lowercase.
* Example:

  ```sql
  SELECT UPPER(city) AS city_upper, LOWER(city) AS city_lower FROM address;
  ```

**3. Numeric Transformations**

* Use standard arithmetic operators (`+`, `-`, `*`, `/`) to modify numeric columns.
* Examples:

  ```sql
  SELECT replacement_cost + 2 AS updated_cost,
         replacement_cost / length AS cost_per_minute
  FROM film;
  ```

**4. Date Transformations**

* Use `EXTRACT()` to pull specific parts (e.g., year, month, hour) from date/time columns.
* Example:

  ```sql
  SELECT EXTRACT(YEAR FROM rental_date) AS rental_year,
         EXTRACT(HOUR FROM rental_date) AS rental_hour
  FROM rental;
  ```

**5. Practice**

* Apply transformations to explore and format data effectively in your own queries.

---

### Working with Aggregate Functions (SQL)

**1. Overview**

* Aggregate functions summarize data across groups of rows.
* Commonly used with the `GROUP BY` clause.

**2. GROUP BY Concept**

* `GROUP BY` divides (partitions) data based on one or more columns.
* Each partition is then summarized using an aggregate function.
* Example:

  ```sql
  SELECT rating, AVG(replacement_cost) AS avg_cost
  FROM film
  GROUP BY rating;
  ```

**3. How It Works**

* `GROUP BY` funnels rows with the same value into one group.
* The aggregate function (e.g., `AVG`) computes a single value per group.

**4. Numeric Aggregate Functions**

* **AVG(column)** → Calculates the mean.
* **COUNT(column)** → Counts the number of rows.
* **SUM(column)** → Adds up numeric values.
* Example:

  ```sql
  SELECT rating,
         AVG(replacement_cost) AS avg_cost,
         COUNT(*) AS film_count,
         SUM(replacement_cost) AS total_cost
  FROM film
  GROUP BY rating;
  ```

**5. String Aggregate Functions**

* **STRING_AGG(column, separator)** → Concatenates string values within a group.
* Example:

  ```sql
  SELECT rating,
         STRING_AGG(title, ', ') AS film_titles
  FROM film
  GROUP BY rating;
  ```

**6. Practice**

* Use different aggregate functions and `GROUP BY` combinations to summarize datasets effectively.

---
