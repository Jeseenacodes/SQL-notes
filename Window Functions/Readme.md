
## **What Are Window Functions?**

A window function performs a calculation *across a set of rows that are related to the current row without collapsing them into one result (unlike `GROUP BY`).

Like, Show me each row, but also tell me how that row compares to others in the same group.

---

## 🔹 1. Basic Syntax

```sql
<function_name>() OVER (
    [PARTITION BY column]
    [ORDER BY column]
    [ROWS BETWEEN ...]
)
```

| Part               | Meaning                                                    | Example                                    |
| ------------------ | ---------------------------------------------------------- | ------------------------------------------ |
| `function_name()`  | The window function                       | `AVG()`, `SUM()`, `ROW_NUMBER()`, etc.     |
| `OVER`             | Tells SQL it’s a window function                           | Mandatory keyword                          |
| `PARTITION BY`     | Optional – divides data into groups (like mini tables)     | `PARTITION BY department_id`               |
| `ORDER BY`         | Optional – defines the order of rows within each partition | `ORDER BY salary DESC`                     |
| `ROWS BETWEEN ...` | Optional – defines exactly which rows to include           | `ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING` |

## 🔹 2. Common Window Functions (Grouped by Type)

### **A. Ranking Functions**

| Function       | What It Does                                                | Example Output |
| -------------- | ----------------------------------------------------------- | -------------- |
| `ROW_NUMBER()` | Assigns a unique rank (no ties)                             | 1, 2, 3, 4...  |
| `RANK()`       | Assigns rank but skips numbers on ties                      | 1, 2, 2, 4...  |
| `DENSE_RANK()` | Like `RANK()` but no skips                                  | 1, 2, 2, 3...  |
| `NTILE(n)`     | Divides rows into `n` groups (percentiles, quartiles, etc.) | 1, 1, 2, 2...  |

 *Use when:* You want to sort or assign ranks, e.g., “Top 3 employees per department.”

### **B. Aggregate Window Functions**

Same functions as aggregates (`SUM`, `AVG`, `MIN`, `MAX`, `COUNT`) but **used with OVER()** so results aren’t collapsed.

| Function                                 | What It Does                  | Example                             |
| ---------------------------------------- | ----------------------------- | ----------------------------------- |
| `SUM(sales) OVER()`                      | Total sales for all rows      | Adds same total in each row         |
| `AVG(salary) OVER(PARTITION BY dept_id)` | Average salary per department | Each row in same dept gets same avg |
| `COUNT(*) OVER()`                        | Total number of rows          | Useful for percentages              |

*Use when:* You want group-level calculations **without losing individual rows.**

### **C. Value Window Functions**

| Function              | Description                   | Example                               |
| --------------------- | ----------------------------- | ------------------------------------- |
| `LAG(column, n)`      | Looks *n rows back*           | Compare this month vs. previous month |
| `LEAD(column, n)`     | Looks *n rows ahead*          | Compare this row vs. next             |
| `FIRST_VALUE(column)` | Gets first value in partition | Get first order date per customer     |
| `LAST_VALUE(column)`  | Gets last value in partition  | Get most recent order date            |

*Use when:* You need comparisons between rows — trends, growth, differences.

## 🔹 3. Example

### Task: Show each employee’s salary, average salary in their department, and their rank.

```sql
SELECT 
    employee_id,
    department_id,
    salary,
    AVG(salary) OVER (PARTITION BY department_id) AS dept_avg_salary,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank_in_dept
FROM employees;
```

Output:

| employee_id | department_id | salary | dept_avg_salary | rank_in_dept |
| ----------- | ------------- | ------ | --------------- | ------------ |
| 101         | 10            | 9000   | 7500            | 1            |
| 102         | 10            | 8000   | 7500            | 2            |
| 103         | 10            | 6000   | 7500            | 3            |

## 🔹 4. Difference Between `GROUP BY` and `OVER()`

| Feature         | `GROUP BY`                 | `OVER()`                           |
| --------------- | -------------------------- | ---------------------------------- |
| Output rows     | One per group              | One per original row               |
| Columns allowed | Only grouped or aggregated | Any column                         |
| Use case        | Summaries                  | Row + summary together             |
| Example         | Avg salary per dept        | Each employee + avg salary in dept |

## 🔹 5. Window Frame Clause (Optional but Powerful)

Used with ordered data — defines which rows to include in a calculation.

Example:

```sql
SUM(sales) OVER (
    ORDER BY sale_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
)
```

Means: For each row, sum sales of the current + 2 previous rows.

## **Summary Table**

| Type             | Function                                    | Use                             |
| ---------------- | ------------------------------------------- | ------------------------------- |
| **Ranking**      | ROW_NUMBER(), RANK(), DENSE_RANK(), NTILE() | Assign row positions            |
| **Aggregate**    | SUM(), AVG(), MIN(), MAX(), COUNT()         | Compute running or group totals |
| **Value-based**  | LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE()  | Compare with other rows         |
| **Clause parts** | PARTITION BY, ORDER BY, ROWS BETWEEN        | Define groups and row scope     |

---

## **Sample Table: `sales`**

| sale_id | region | salesperson | total_sales |
| ------- | ------ | ----------- | ----------- |
| 1       | East   | Alice       | 5000        |
| 2       | East   | Bob         | 7000        |
| 3       | East   | Carol       | 7000        |
| 4       | East   | Dan         | 3000        |
| 5       | West   | Eva         | 8000        |
| 6       | West   | Frank       | 6000        |
| 7       | West   | Grace       | 4000        |

## **Goal:**

Rank salespeople *within their region* by `total_sales` and compare each person’s sales with their next and previous one.

### **Query Using All Key Window Functions**

```sql
SELECT region, salesperson, total_sales,
    ROW_NUMBER() OVER ( PARTITION BY region ORDER BY total_sales DESC ) AS row_number,

    RANK() OVER ( PARTITION BY region ORDER BY total_sales DESC ) AS rank_value,

    DENSE_RANK() OVER ( PARTITION BY regionORDER BY total_sales DESC ) AS dense_rank_value,

    LAG(total_sales) OVER (PARTITION BY region ORDER BY total_sales DESC ) AS prev_sales,

    LEAD(total_sales) OVER ( PARTITION BY region ORDER BY total_sales DESC ) AS next_sales,

    NTILE(3) OVER (  PARTITION BY region ORDER BY total_sales DESC ) AS performance_group

FROM sales;
```

## ** Breakdown**

| region | salesperson | total_sales | row_number | rank_value | dense_rank | prev_sales | next_sales | performance_group |
| :----- | :---------- | ----------: | ---------: | ---------: | ---------: | ---------: | ---------: | ----------------: |
| East   | Bob         |        7000 |          1 |          1 |          1 |   *(null)* |       7000 |                 1 |
| East   | Carol       |        7000 |          2 |          1 |          1 |       7000 |       5000 |                 1 |
| East   | Alice       |        5000 |          3 |          3 |          2 |       7000 |       3000 |                 2 |
| East   | Dan         |        3000 |          4 |          4 |          3 |       5000 |   *(null)* |                 3 |
| West   | Eva         |        8000 |          1 |          1 |          1 |   *(null)* |       6000 |                 1 |
| West   | Frank       |        6000 |          2 |          2 |          2 |       8000 |       4000 |                 2 |
| West   | Grace       |        4000 |          3 |          3 |          3 |       6000 |   *(null)* |                 3 |

## **Understanding Each Column**

| Function       | What it shows                                                                        |
| -------------- | ------------------------------------------------------------------------------------ |
| `ROW_NUMBER()` | Unique sequence within region (no ties)                                              |
| `RANK()`       | Same rank for ties, skips next rank                                                  |
| `DENSE_RANK()` | Same rank for ties, **no skips**                                                     |
| `LAG()`        | Value of the **previous salesperson’s sales** in same region                         |
| `LEAD()`       | Value of the **next salesperson’s sales**                                            |
| `NTILE(3)`     | Divides each region’s salespeople into **3 equal groups** (1 = top tier, 3 = bottom) |

## Summary

* `ROW_NUMBER` → unique numbering
* `RANK` → tie-sensitive with gaps
* `DENSE_RANK` → tie-sensitive without gaps
* `LAG` / `LEAD` → compare rows (previous / next)
* `NTILE(n)` → split into percentiles or performance groups

---




Would you like me to make this into a **black-and-white visual cheat sheet** like the subquery one (clear layout with one-page summary)?
