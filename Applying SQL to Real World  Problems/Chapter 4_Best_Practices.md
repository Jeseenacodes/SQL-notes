### Convey Your Intent (SQL Best Practices)

**1. Purpose of the Lesson**

* Focuses on **writing clear, readable SQL scripts** — not just code that works.
* Emphasizes making your intent obvious to others (and your future self).

**2. Why Readability Matters**

* Six months later, you or someone else may need to **understand or modify** your code.
* Clean, intentional SQL saves time and prevents confusion or mistakes.

**3. Key Techniques to Convey Intent**

**a. Use `AS` for Clarity**

* Always use the `AS` keyword when assigning aliases to tables or columns.

  ```sql
  SELECT title AS film_title
  FROM film;
  ```
* Avoid ambiguity — makes it clear that `film_title` is a renamed column, not a separate one.

**b. Specify the Type of JOIN**

* Write out the full join type (`INNER JOIN`, `LEFT JOIN`, etc.) instead of just `JOIN`.

  ```sql
  SELECT ...
  FROM customer
  INNER JOIN rental ON ...
  ```
* Makes the logic explicit and easier for readers to follow.

**c. Use Meaningful Aliases**

* Avoid arbitrary names like `x1`, `x2`.
* Use short, meaningful aliases — e.g., `c` for `customer`, `r` for `rental`, or `cust` for `customer`.
* Clear aliases improve understanding when working with multiple tables.

**d. Add Comments**

* Use comments to explain logic, data quirks, or complex joins.

  ```sql
  /* Multi-line comment starts with slash-star */
  -- Single-line comment starts with two dashes
  ```
* Helps document reasoning and makes the code self-explanatory.

**4. Key Takeaway**
→ The goal is not just to make your SQL work — but to make your **intent clear** through proper naming, explicit joins, and meaningful comments.

---

###  Write Readable SQL Code

**1. Purpose of the Lesson**

* Building on the previous lesson about conveying intent, this chapter focuses on **making SQL code visually clear and easy to read**.
* Readability is essential as SQL scripts can grow long and complex.

**2. Best Practices for Readable SQL**

**a. Capitalize SQL Commands**

* Write SQL keywords (e.g., `SELECT`, `FROM`, `WHERE`, `JOIN`) in **UPPERCASE**, and everything else in **lowercase**.
* Helps distinguish commands from columns and tables, even without syntax highlighting.

**b. Use New Lines & Indentation**

* Place each column or condition on its **own line**.
* Use **indentation** for readability and structure.

  ```sql
  SELECT 
      first_name,
      last_name
  FROM customer
  WHERE 
      active = TRUE
      AND city = 'London';
  ```
* Improves clarity and helps readers quickly see query logic.

**c. Use `snake_case` for Names**

* Avoid spaces in table and column names; use underscores (`_`) instead.

  * Example: `customer_name` instead of `CustomerName` or `customer name`.
* Enhances consistency and readability.

**d. Use `IN` Instead of Many `OR` Statements**

* Simplify multiple `OR` conditions by using `IN`.

  ```sql
  WHERE city IN ('London', 'Paris', 'Berlin');
  ```
* More concise and readable.

**e. Use `BETWEEN` for Range Conditions**

* Replace multiple inequalities with the `BETWEEN` command.

  ```sql
  WHERE amount BETWEEN 10 AND 50;
  ```
* Makes numeric range filters cleaner and easier to interpret.

**3. Key Takeaway**
→ **Readable SQL = Maintainable SQL.**
Following these five strategies makes your queries cleaner, clearer, and much easier to debug or share.

---

### Avoid Common SQL Mistakes

**1. Purpose of the Lesson**

* Learn the most frequent mistakes developers make when writing SQL scripts and how to avoid them.
* Focus on improving readability, maintainability, and performance.

**2. Common Mistakes to Avoid**

**a. Misusing Comments**

* ✅ *Do:* Use comments to clarify logic or highlight non-obvious steps.
* ❌ *Don’t:*

  * Write long “essays” in comments — keep them **concise and relevant**.
  * Leave **outdated or leftover comments** from prototyping.
  * Repeat the **same logic in plain English** — redundant comments reduce clarity.
* Poor commenting can make simple queries harder to understand.

**b. Using `SELECT *` (Selecting Everything)**

* Avoid selecting all columns unless absolutely necessary.
* Specify only the **columns you actually need**.

  * Improves **query clarity**, **performance**, and **downstream efficiency**.

  ```sql
  SELECT customer_id, first_name, last_name
  FROM customer;
  ```

**c. Using SQL as a Programming Language**

* SQL is designed for **data management**, not for full-fledged programming.
* Avoid embedding **loops, conditionals, or print statements** in SQL.
* For logic-heavy tasks, use appropriate languages like **Python, R, or Java**.

**3. Key Takeaway**
→ **Keep SQL focused, clean, and efficient.**
Use SQL for querying and managing data, not as a general-purpose programming tool.
Write concise comments, select only needed data, and keep your scripts simple.

---
