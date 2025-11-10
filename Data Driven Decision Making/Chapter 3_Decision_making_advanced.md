### **Topic: Nested Queries (Subqueries in WHERE and HAVING Clauses)**

Learn how to use **nested queries** — subqueries placed inside the `WHERE` or `HAVING` clauses — to make SQL queries more powerful and dynamic.

### **1. What Is a Nested Query?**

A **nested query** is a complete `SELECT` statement that appears **inside** another query’s `WHERE` or `HAVING` clause.

* The **inner query** executes first.
* Its result(s) are then used by the **outer query**.
* The inner query may return **a single value** or **multiple values**.

### **2. Example: Disappointed Customers**

Goal: Find customers who gave a rating ≤ 3.

* **Inner query:**

  ```sql
  SELECT DISTINCT customer_id
  FROM renting
  WHERE rating <= 3;
  ```

  → returns a list of customer IDs.

* **Outer query:**

  ```sql
  SELECT name
  FROM customers
  WHERE customer_id IN (
      SELECT DISTINCT customer_id
      FROM renting
      WHERE rating <= 3
  );
  ```

  → retrieves names of customers who were disappointed with their movies.

 Use `IN` when the inner query returns **multiple values**.

### **3. Nested Query in HAVING Clause**

Goal: Find countries where accounts were created **earlier than in Austria**.

* **Inner query:** finds the earliest account creation date in Austria.
* **Outer query:** groups customers by country, then uses `HAVING` to compare dates.

```sql
HAVING MIN(account_created) < (
    SELECT MIN(account_created)
    FROM customers
    WHERE country = 'Austria'
);
```

### **4. Multi-Level Nesting Example: “Who Are the Actors in the Movie *Ray*?”**

Nested queries can have **multiple levels**:

1. Inner-most query → gets movie_id for 'Ray'.
2. Middle query → retrieves actor_ids for that movie.
3. Outer query → returns actor names for those IDs.

```sql
SELECT name
FROM actors
WHERE actor_id IN (
    SELECT actor_id
    FROM actsin
    WHERE movie_id = (
        SELECT movie_id
        FROM movies
        WHERE name = 'Ray'
    )
);
```

### **Key Takeaways**

* Nested queries **add flexibility** by allowing results from one query to feed another.
* Use **`IN`** when expecting multiple values, and **comparison operators (`=`, `<`, `>`)** for single values.
* They can appear in both **`WHERE`** and **`HAVING`** clauses.
* Enable **multi-step logical analysis** within a single SQL command.

---

### **Topic: Correlated Nested Queries**
Understand how **correlated subqueries** differ from regular (independent) subqueries and how they allow comparisons between rows across related tables.

### **1. What Is a Correlated Query?**

A **correlated nested query** is a subquery that **depends on values from the outer query**.

* The **inner query** references a column from the **outer query’s table**.
* It cannot be executed independently because it’s re-evaluated **for each row** of the outer query.
* Acts like a **loop**, running the inner query repeatedly for every record in the outer query’s result set.

### **2. Example: Movies Rented More Than 5 Times**

Goal: Identify movies that were rented more than five times.

```sql
SELECT m.movie_id, m.title
FROM movies AS m
WHERE 5 < (
    SELECT COUNT(*)
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
);
```

🔹 The inner query counts how many times each movie was rented (`COUNT(*) FROM renting`).
🔹 The outer query checks if that count exceeds 5.
🔹 The subquery runs **once per movie**, returning only those with >5 rentals.

Result: Example — *“One Night at McCool’s”* (rented 8 times).

### **3. Inverse Example: Movies Rented Fewer Than 5 Times**

Switching the condition identifies underperforming movies:

```sql
SELECT m.movie_id, m.title
FROM movies AS m
WHERE 5 > (
    SELECT COUNT(*)
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
);
```

Result: Example — *“The Human Stain”* (rented fewer than 5 times).

### **4. Key Takeaways**

* A **correlated subquery** references the **outer query** table.
* Executes **repeatedly**, once for each row of the outer query.
* Useful for **row-by-row comparisons** or **conditional aggregation**.
* Acts conceptually like a **loop** that filters based on dynamic criteria.

---

### **Topic: Queries with EXISTS**

Learn how to use the **EXISTS** and **NOT EXISTS** functions in SQL to check whether related data exists in another table — a powerful feature for filtering data in **correlated subqueries**.

### **1. What Is EXISTS?**

* **EXISTS** is a **special case of a correlated nested query**.
* It returns a **boolean value** — `TRUE` if the subquery produces at least one row, `FALSE` if it returns none.
* The **outer query** includes a row **only if** the condition in the **EXISTS** subquery is `TRUE`.
* Because it only checks for existence, the **SELECT** clause in the subquery usually just uses `SELECT *`.

### **2. Example: Movies with At Least One Rating**

```sql
SELECT m.movie_id, m.title
FROM movies AS m
WHERE EXISTS (
    SELECT *
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
      AND r.rating IS NOT NULL
);
```

🔹 The inner query checks if any rentals exist for a movie where a rating is not null.
🔹 If the inner query returns any rows, the movie is included.
🔹 Example: Movie with ID 1 (rated at least once) appears in the result; movie ID 11 (no ratings) does not.

### **3. Using NOT EXISTS**

To find **movies without any ratings**, simply reverse the condition:

```sql
SELECT m.movie_id, m.title
FROM movies AS m
WHERE NOT EXISTS (
    SELECT *
    FROM renting AS r
    WHERE r.movie_id = m.movie_id
      AND r.rating IS NOT NULL
);
```

🔹 `NOT EXISTS` returns `TRUE` if the subquery result is empty.
🔹 Example: *“Showtime”* (movie ID 11) appears in the output since it has no ratings.

### **4. Key Takeaways**

* **EXISTS** → Returns TRUE if at least one matching record exists.
* **NOT EXISTS** → Returns TRUE if no matching records exist.
* Commonly used for:

  * Validating **data relationships** (e.g., customers with/without purchases)
  * Ensuring **referential completeness**
  * Efficiently filtering results without joining large tables

---

### **Topic: Queries with UNION and INTERSECT**

---

###  **Objective**

Learn how to combine or compare result sets from multiple SQL queries using **set operators** — specifically **UNION** and **INTERSECT**.

---

### 🔹 **1. UNION**

* Combines the results of **two or more SELECT queries** into one table.
* Returns **all distinct rows** that appear in **either** query’s result set (duplicates removed).
* Each SELECT query must have the **same number of columns**, in the **same order** and with **compatible data types**.

**Example:**

```sql
SELECT title, genre, renting_price
FROM movies
WHERE renting_price > 2.8

UNION

SELECT title, genre, renting_price
FROM movies
WHERE genre IN ('Action', 'Adventure');
```

 **Result:**
All movies that either

* have a renting price > 2.8, **or**
* belong to the Action or Adventure genres.
  Duplicates are automatically removed.

---

### 🔹 **2. INTERSECT**

* Returns **only the rows that appear in both** result sets.
* Like UNION, the queries must have the same column structure.
* Used to find **common records** between two datasets.

**Example:**

```sql
SELECT title, genre, renting_price
FROM movies
WHERE renting_price > 2.8

INTERSECT

SELECT title, genre, renting_price
FROM movies
WHERE genre IN ('Action', 'Adventure');
```

**Result:**
Only movies that **satisfy both conditions** — e.g., *“Astro Boy”*, which is both Action/Adventure and priced above 2.8.

---

### **3. Key Takeaways**

* **UNION** → Combines datasets (A ∪ B).
* **INTERSECT** → Finds overlap between datasets (A ∩ B).
* Both eliminate duplicates unless you use `UNION ALL` or `INTERSECT ALL`.
* Ensure **column alignment** (same count, order, and type) in all queries.

---
