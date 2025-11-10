

### **Topic: Grouping and Aggregating Data with `GROUP BY`**

The `GROUP BY` clause allows you to **summarize data within categories or segments**, enabling deeper insights — e.g., comparing average movie prices by genre or customer counts by country.

### **Why Group Data?**

Grouping helps analyze business performance across **customer segments** or **product categories**, such as:

* Average rental price **per movie genre**
* Customer activity **by country or gender**
* Movie popularity **by release year**

### **Using `GROUP BY`**

* `GROUP BY` groups rows sharing the same value in one or more columns.
* Aggregation functions (e.g., `AVG`, `SUM`, `COUNT`, `MIN`, `MAX`) can then be applied to each group.

*Example:*
```sql
SELECT genre, AVG(renting_price) AS avg_price
FROM movies_selected
GROUP BY genre;
```

This returns the **average rental price** for each **movie genre**.

### **How GROUP BY Works**

* Think of it as splitting the table into **subtables per group** (e.g., all “Drama” movies together).
* You can then apply aggregate functions like:

  ```sql
  SELECT genre,
         AVG(renting_price) AS avg_price,
         COUNT(*) AS movie_count
  FROM movies_selected
  GROUP BY genre;
  ```

  → Shows each genre with its **average price** and **number of movies**.

### **Filtering Groups with `HAVING`**

* The `HAVING` clause filters **aggregated results**, unlike `WHERE`, which filters individual rows.
 *Example:*

```sql
SELECT genre, COUNT(*) AS movie_count
FROM movies_selected
GROUP BY genre
HAVING COUNT(*) > 2;
```

→ Returns only genres with **more than 2 movies**.

### **Key Takeaway**

`GROUP BY` turns raw data into **category-level insights**, helping identify top-performing groups, trends, and areas of opportunity.
`HAVING` refines those insights by focusing on groups meeting specific criteria.

---

### **Topic: Joining Data Across Multiple Tables**

To perform deeper analysis, we often need to **combine related data** from different tables.
**JOIN** operations allow us to merge datasets using shared identifiers (keys) — for example, linking **customer details** to their **movie rentals and ratings**.


### **LEFT JOIN Overview**

* A **LEFT JOIN** keeps **all rows** from the left table and matches data from the right table **where possible**.
* If no match exists, the result shows **NULL values** for columns from the right table.
* Used when you don’t want to lose records from the main (left) dataset.

*Example:*

```sql
SELECT *
FROM renting_selected AS r
LEFT JOIN customers_selected AS c
ON r.customer_id = c.customer_id;
```

→ Adds customer info to each rental record.
→ Customers without rentals are excluded; rentals without matching customer info show `NULL`s.

### **Using Table Aliases**

* Aliases make queries shorter and clearer.
* Format:

  ```sql
  FROM customers AS c
  SELECT c.customer_id, c.name
  ```
* Helpful when multiple tables share the same column names (e.g., `customer_id`).

### **Joining Multiple Tables**

To combine **customer**, **movie**, and **rental** information:

```sql
SELECT c.name, m.title, r.rating
FROM renting AS r
LEFT JOIN movies AS m ON r.movie_id = m.movie_id
LEFT JOIN customers AS c ON r.customer_id = c.customer_id;
```

→ Shows which **customer** watched which **movie**, along with their **rating**.

### **Key Takeaway**

* **JOINs** create complete, contextual views of business data.
* **LEFT JOIN** ensures no data loss from the primary table.
* **Multiple joins** enable rich insights by connecting customers, products, and transactions.

---

### **Topic: Calculating Money Spent per Customer Using Subqueries**

A **subquery** (or **sub-select**) allows you to use the result of one query **as a temporary table** inside another query.
This is powerful for **multi-step analyses**, such as calculating how much each customer spent on movie rentals.

### **Step 1: Understanding Subqueries**

* A subquery is placed **inside parentheses** and given an **alias**.
* The **inner query** runs first, producing a result that the **outer query** then uses.
  *Structure Example:*

```sql
SELECT nationality,
       MIN(year_of_birth) AS oldest,
       MAX(year_of_birth) AS youngest
FROM (
    SELECT * FROM actors WHERE gender = 'F'
) AS af
GROUP BY af.nationality;
```

→ The inner query filters actresses; the outer query groups them by nationality.

### **Step 2: Join Data for Spending Calculation**

To calculate spending per customer, first join **renting** and **movies** tables to access rental prices:

```sql
SELECT r.customer_id, m.renting_price
FROM renting AS r
JOIN movies AS m ON r.movie_id = m.movie_id;
```

### 🧾 **Step 3: Use a Subquery to Aggregate Spending**

Wrap the first query as a subquery and group results by customer:

```sql
SELECT customer_id,
       SUM(renting_price) AS total_spent
FROM (
    SELECT r.customer_id, m.renting_price
    FROM renting AS r
    JOIN movies AS m ON r.movie_id = m.movie_id
) AS rm
GROUP BY customer_id;
```

→ Returns the **total amount each customer spent** on movie rentals.

### **Key Takeaway**

Subqueries let analysts:

* Break down complex problems into logical steps.
* Perform **aggregations on derived tables**.
* Create **layered, readable SQL workflows** for business insights — such as customer value analysis.

---

### **Topic: Identifying Favorite Actors by Customer Group**

Learn how to **combine multiple SQL clauses**—`LEFT JOIN`, `WHERE`, `GROUP BY`, `HAVING`, and `ORDER BY`—to analyze customer preferences and identify **favorite actors** for different customer segments.

### **Step 1: Build the Data Foundation**

To find favorite actors, we first need to **combine data from multiple tables**:

1. Start with the `renting` table (records of who rented what).
2. Join with the `actsin` table using `movie_id` to connect movies and actors.
3. Join the resulting table with `actors` using `actor_id`.
4. Add customer data by joining with the `customers` table.

*Result:* A single dataset linking **customers → movies → actors**.

### **Step 2: Filter for a Specific Customer Group**

Example: Analyze **male customers** only.

```sql
WHERE c.gender = 'male'
```

### **Step 3: Aggregate and Measure Popularity**

Count how often each actor’s movies were watched:

```sql
GROUP BY a.name
COUNT(*) AS total_views
```

Optionally, measure **average movie rating** per actor:

```sql
AVG(rating) AS avg_rating
```

### **Step 4: Refine Results with HAVING and ORDER BY**

* Use `HAVING avg_rating IS NOT NULL` to exclude actors without ratings.
* Use `ORDER BY avg_rating DESC, total_views DESC` to rank favorites — first by best ratings, then by popularity.

### **Result**

Finds the top actors among specific customer groups — for example:

> Ray Romano emerged as the favorite actor among male customers, achieving an average rating of **10** with **3 views**.

### **Key Takeaways**

* **`WHERE`** filters data **before aggregation**,
  **`HAVING`** filters **after aggregation**.
* Combining multiple clauses enables **rich behavioral insights**.
* Ideal for **customer segmentation analysis** and **preference modeling** in SQL.

---

