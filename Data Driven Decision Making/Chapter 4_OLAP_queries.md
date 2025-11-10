
###  **Topic: OLAP – The CUBE Operator**

Understand how **OLAP (Online Analytical Processing)** and the **CUBE operator** are used in SQL to generate **multi-dimensional summaries** — similar to Excel Pivot Tables — for business intelligence analysis.

### 🔹 **1. Introduction to OLAP**

* **OLAP** allows interactive, multi-dimensional analysis of data.
* Common use cases include:

  * Counting how many movies each customer rented
  * Finding the **average rating** of movies by **genre** and **country**
* OLAP helps summarize data across **different aggregation levels** to identify patterns and trends.
* SQL operators used: **CUBE**, **ROLLUP**, and **GROUPING SETS**.
* These operators produce results comparable to **pivot tables** in BI tools.

### 🔹 **2. Example Dataset – `rentings_extended`**

* Simplified dataset containing:

  * `country` (Austria or Belgium)
  * `genre` (Drama or Comedy)
* Used to demonstrate data aggregation at various levels.

### 🔹 **3. Pivot Table Example**

* Aggregation of **number of movie rentals** by `country` and `genre`.
* Example structure:

| Country | Genre  | Count |
| ------- | ------ | ----- |
| Austria | Drama  | ...   |
| Austria | Comedy | ...   |
| Belgium | Drama  | ...   |
| Belgium | Comedy | ...   |

* Totals:

  * **By Country** → Vertical aggregation (e.g., total rentals in Austria)
  * **By Genre** → Horizontal aggregation (e.g., total Drama rentals)
  * **Grand Total** → Overall count (e.g., 22 rentals total)

### 🔹 **4. SQL Representation**

Each pivot table value corresponds to one SQL row:

* 4 rows for unique `(country, genre)` combinations
* 2 rows where `genre` = NULL → totals per country
* 2 rows where `country` = NULL → totals per genre
* 1 row with both NULL → grand total

### 🔹 **5. GROUP BY CUBE Syntax**

The **CUBE operator** automates all these aggregation levels in a single query.

**Example:**

```sql
SELECT 
    country,
    genre,
    COUNT(*) AS total_rentals
FROM rentings_extended
GROUP BY CUBE(country, genre);
```

**Result:**

* Returns all possible aggregations:

  * By `(country, genre)`
  * By `country` only
  * By `genre` only
  * Grand total (all rows)

### 🔹 **6. Variations**

To count only **non-null ratings**:

```sql
SELECT 
    country,
    genre,
    COUNT(rating) AS total_ratings
FROM rentings_extended
GROUP BY CUBE(country, genre);
```

This yields smaller counts since NULL ratings are excluded.

###  **7. Key Takeaways**

* **CUBE** generates **all combinations of aggregations** automatically.
* Great for **multidimensional summaries** (e.g., by region, category, time).
* Reduces need for multiple GROUP BY queries.
* Ideal for **pivot table-like BI reporting** directly in SQL.

---

### **Topic: OLAP – The ROLLUP Operator**

Learn how to use the **ROLLUP operator** in SQL to generate **hierarchical aggregations** — summarizing data at multiple levels of detail for business reporting and analysis.


### 🔹 **1. What is ROLLUP?**

* The **ROLLUP operator** is an OLAP extension used with `GROUP BY` to create **hierarchical summaries** of data.
* It produces **subtotals and grand totals** — similar to an Excel Pivot Table with grouped totals.
* Syntax requires **parentheses** around the grouped columns.

  ```sql
  GROUP BY ROLLUP(country, genre)
  ```

### 🔹 **2. Example Dataset**

* Uses the table **`renting_extended`**, which includes:

  * `country` (e.g., Austria, Belgium)
  * `genre` (e.g., Drama, Comedy)
  * rental and rating details
* The goal is to calculate total movie rentals across different aggregation levels.

### 🔹 **3. Query Example**

```sql
SELECT 
    country,
    genre,
    COUNT(*) AS n_rentals
FROM renting_extended
GROUP BY ROLLUP(country, genre);
```

📊 **Result Includes:**

1. Rentals for each **(country, genre)** pair
2. Rentals **per country** (genre = NULL)
3. **Total rentals** (both country and genre = NULL)

---

### 🔹 **4. Importance of Column Order**

* The **order of columns in ROLLUP matters**:

  * `ROLLUP(country, genre)` → aggregates by country first, then totals for all countries.
  * `ROLLUP(genre, country)` → aggregates by genre first, then totals by all genres.
* Unlike **CUBE**, ROLLUP does **not** return all combinations — only **hierarchical** ones.


### 🔹 **5. ROLLUP vs. CUBE**

| Feature        | ROLLUP                                                        | CUBE                                   |
| -------------- | ------------------------------------------------------------- | -------------------------------------- |
| Purpose        | Hierarchical aggregation                                      | All combinations of aggregation        |
| Column Order   | Important                                                     | Not important                          |
| Example Output | Subtotals + Grand Total                                       | Subtotals + Cross Totals + Grand Total |
| Use Case       | Drilling up/down in hierarchy (e.g., Country → Genre → Total) | Full multi-dimensional summaries       |

### 🔹 **6. Multiple Aggregations**

You can compute more than one measure:

```sql
SELECT 
    country,
    genre,
    COUNT(*) AS n_rentals,
    COUNT(rating) AS n_ratings
FROM renting_extended
GROUP BY ROLLUP(country, genre);
```
 Returns both **number of rentals** and **number of ratings** across all rollup levels.


### **7. Key Takeaways**

* **ROLLUP** creates **progressive aggregation levels** — ideal for **hierarchical data** (e.g., Region → Country → Total).
* **Column order defines aggregation depth**.
* Simpler than CUBE, used when **hierarchical summarization** (not all combinations) is needed.

---

Here’s a clear and portfolio-ready summary of the **“OLAP: GROUPING SETS Operator”** lesson:

---

### **Topic: OLAP – GROUPING SETS Operator**

Understand how to use the **GROUPING SETS operator** to perform **customized multi-level aggregations** — offering more flexibility than `CUBE` and `ROLLUP`.

### 🔹 **1. What is GROUPING SETS?**

* The **most flexible OLAP operator** in SQL.
* Allows you to **explicitly define which aggregations** you want to include in your result set.
* Works like a **UNION of multiple GROUP BY queries** — combining their results into a single output.

### 🔹 **2. Example Dataset**

* Uses the table **`renting_extended`** with columns:

  * `country` (e.g., Austria, Belgium)
  * `genre` (e.g., Drama, Comedy)
  * rental and rating information
* The goal: summarize movie rentals at various aggregation levels.

### 🔹 **3. Syntax & Example**

```sql
SELECT 
    country,
    genre,
    COUNT(*) AS n_rentals
FROM renting_extended
GROUP BY GROUPING SETS (
    (country, genre),
    (country),
    (genre),
    ()
);
```

📊 **This query produces:**

1. Rentals by **country + genre**
2. Rentals by **country**
3. Rentals by **genre**
4. The **overall total**

Each grouping level corresponds to one of the parentheses sets.

### 🔹 **4. Conceptual View**

* Equivalent to executing four separate `GROUP BY` queries and **merging** their results:

  * `GROUP BY country, genre`
  * `GROUP BY country`
  * `GROUP BY genre`
  * `GROUP BY ()` (total)
* The **empty parentheses** `()` represent the **grand total** aggregation.

### 🔹 **5. Output Interpretation**

* Each row represents one aggregation level.
* **NULL values** in `country` or `genre` mean that column was **aggregated** (not grouped).
* Order of results may differ from the order in the `GROUPING SETS` clause.


### 🔹 **6. Multiple Aggregations**

You can include more than one aggregated measure:

```sql
SELECT 
    country,
    genre,
    COUNT(*) AS n_rentals,
    AVG(rating) AS avg_rating
FROM renting_extended
GROUP BY GROUPING SETS (
    (country, genre),
    (genre)
);
```

Returns both **count of rentals** and **average ratings** for each defined grouping.

### 🔹 **7. Comparison: OLAP Operators**

| Feature          | CUBE                      | ROLLUP                 | GROUPING SETS            |
| ---------------- | ------------------------- | ---------------------- | ------------------------ |
| Aggregation type | All combinations          | Hierarchical           | Custom-defined           |
| Flexibility      | High                      | Moderate               | **Highest**              |
| Column order     | Not important             | Important              | Not important            |
| Best for         | Full pivot-like summaries | Drill-down hierarchies | Custom aggregation logic |


### **8. Key Takeaways**

* `GROUPING SETS` = **manual control** over which aggregations to include.
* Ideal when you want **specific summaries** without computing every possible combination (like CUBE).
* Supports **multiple metrics** such as counts, sums, and averages simultaneously.

---

### **Final Project: MovieNow Business Case**

### **Goal**

Support **management decision-making** by analyzing whether **customers prefer newer movies** and if these preferences vary **by country** — to guide investment in new movie acquisitions.

---

### 🔹 **1. Business Context**

* **MovieNow**, a movie rental company, is considering investing in **newer films**.
* **Challenge:** New movies are **more expensive** to license, so the company must determine if **recent releases receive better customer ratings**.

### 🔹 **2. Step 1 – Join the Data**

Combine data from multiple sources:

* `renting` → customer ratings
* `customers` → customer country
* `movies` → year of release

**Goal:** Create a unified dataset with movie ratings, release year, and customer location.

### 🔹 **3. Step 2 – Filter Relevant Records**

Apply **data restrictions** to focus on meaningful insights:

* Include only **movies with ≥ 4 ratings**

  ```sql
  WHERE movie_id IN (
      SELECT movie_id 
      FROM renting 
      WHERE rating >= 4
  )
  ```
* Include **rentals from April 1st, 2018 onward** to ensure data recency.

### 🔹 **4. Step 3 – Aggregate the Data**

Perform **multi-level aggregation** using `ROLLUP` to summarize the data hierarchically.

**Metrics computed:**

* Total number of rentals (`COUNT(*)`)
* Number of distinct movies (`COUNT(DISTINCT movie_id)`)
* Average customer rating (`AVG(rating)`)

**SQL Structure:**

```sql
SELECT 
    year_of_release,
    country,
    COUNT(*) AS n_rentals,
    COUNT(DISTINCT movie_id) AS n_movies,
    AVG(rating) AS avg_rating
FROM joined_data
WHERE ...
GROUP BY ROLLUP (year_of_release, country)
ORDER BY country, year_of_release;
```

This query generates:

1. **Overall totals**
2. **Aggregations by release year**
3. **Aggregations by year and country**

### 🔹 **5. Results & Insights**

Example output:

| Year of Release | Country          | Rentals | Movies | Avg Rating |
| --------------- | ---------------- | ------- | ------ | ---------- |
| 2016            | Austria          | 45      | 12     | 8.1        |
| 2017            | Belgium          | 52      | 15     | 7.8        |
| **NULL**        | **NULL (Total)** | **333** | **50** | **7.9**    |

**Interpretation:**

* No clear trend showing that **newer movies receive higher ratings**.
* Suggests **customer preference is not strongly linked** to movie release year.

### **6. Key Learnings**

* Demonstrated the **full SQL analysis workflow**:

  * Joining multiple tables
  * Using **nested queries** for filtering
  * Applying **OLAP aggregation** via `ROLLUP`
  * Summarizing data for **strategic business insights**
* The query structure supports both **operational reporting** and **strategic decision-making**.

---

