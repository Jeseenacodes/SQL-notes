
### **1️⃣ Scalar Subqueries**

**Definition:**
A **scalar subquery** is a subquery (a query inside another query) that **returns only one single value** — just one cell (one row, one column).

You can use it **inside SELECT, WHERE, or HAVING** clauses like a normal value.

**Example:**

```sql
SELECT name, age
FROM patients
WHERE age > (SELECT AVG(age) FROM patients);
```
The inner query `(SELECT AVG(age) FROM patients)` returns **one single number** — the average age.
So, the outer query lists patients older than that average.

**Think of it like a single-value lookup**.

---

### **2️⃣ Multi-row and Multi-column Subqueries**

**Definition:**

* A **multi-row subquery** returns **multiple rows** (but one column).
* A **multi-column subquery** returns **multiple columns and rows**.

**You handle multi-row subqueries** with operators like `IN`, `ANY`, or `ALL`.

**Example (multi-row):**

```sql
SELECT name
FROM doctors
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'New York');
```
The inner query returns **a list of department IDs**, so the outer query finds doctors in any of those departments.

**Example (multi-column):**

```sql
SELECT name, dept_id
FROM doctors
WHERE (dept_id, salary) IN (SELECT dept_id, salary FROM senior_doctors);
```
 Here the subquery returns **two columns (dept_id, salary)** — both must match for the condition to be true.

---

### **3️⃣ Nested and Correlated Subqueries**

**Definition:**

* A **nested subquery** (also called a simple subquery) runs **independently** — the inner query runs first, and its result is used by the outer one.
* A **correlated subquery** depends on the **outer query** — it runs **once for each row** in the outer query.

**Example (nested):**

```sql
SELECT name
FROM patients
WHERE city IN (SELECT city FROM hospitals WHERE rating > 4);
```

The inner query runs first to get cities, then the outer query runs next.

**Example (correlated):**

```sql
SELECT name
FROM patients p
WHERE age > (SELECT AVG(age) FROM patients WHERE city = p.city);
```

The inner query depends on each patient’s `city` — it’s re-run for each row.
Correlated = “connected” to outer query.

---

### **4️⃣ Common Table Expressions (CTEs)**

**Definition:**
A **CTE** is like a **temporary named result** you can reuse in your main query.
It makes queries easier to read and organize, especially when using subqueries multiple times.

**Syntax:**

```sql
WITH cte_name AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM doctors
    GROUP BY dept_id
)
SELECT d.name, d.salary, c.avg_salary
FROM doctors d
JOIN cte_name c ON d.dept_id = c.dept_id;
```

The `WITH` part defines the CTE (a temporary result).
The main query then uses it just like a table.

Think of it as a **temporary, reusable subquery** with a name.

---

### **5️⃣ Views in SQL**

**Definition:**
A **view** is like a **saved SQL query** that behaves like a table.
You can use it again without rewriting the query every time.

**Example:**

```sql
CREATE VIEW high_value_patients AS
SELECT name, total_bill
FROM patients
WHERE total_bill > 5000;

-- Now use it like a table
SELECT * FROM high_value_patients;
```

The view stores the SQL logic, not the data itself (unless it’s a materialized view).
Think of it as a **virtual table** built from a query.

---

### ✅ Summary Chart

| Concept                   | Returns               | Used For        | Example Keywords       | Easy Meaning            |
| ------------------------- | --------------------- | --------------- | ---------------------- | ----------------------- |
| **Scalar Subquery**       | 1 value               | Comparisons     | `=`, `<`, `>`          | Single value result     |
| **Multi-row Subquery**    | Many rows (1 column)  | Lists           | `IN`, `ANY`, `ALL`     | Returns multiple values |
| **Multi-column Subquery** | Many rows + columns   | Matching pairs  | `(col1, col2)`         | Returns table-like data |
| **Correlated Subquery**   | Varies per row        | Row-wise checks | Uses outer query alias | Depends on each row     |
| **CTE**                   | Temporary named query | Clean structure | `WITH ... AS (...)`    | Reusable subquery       |
| **View**                  | Saved query           | Reuse long SQL  | `CREATE VIEW`          | Virtual table           |

---
