### Find the Right Table :

**1. Overview**

* This chapter focuses on **how to locate the data** you need in a database — an essential skill when dealing with large or unfamiliar databases.

**2. Knowing Your Data Sources**

* In real-world scenarios, you may not know:

  * Which **tables** exist in the database.
  * Which **columns** contain the data you need.
* Learning how to explore and identify tables is as important as querying them.

**3. Exploring Tables Manually**

* Start by checking what columns exist in each table.
* Use a simple query to preview data:

  ```sql
  SELECT * FROM table_name;
  ```

**4. Use the LIMIT Command**

* Always use `LIMIT` when exploring large tables to avoid performance issues.

  ```sql
  SELECT * FROM table_name LIMIT 5;
  ```
* Helps you quickly inspect data structure without retrieving all rows.

**5. Listing All Tables in a Database**

* You can query system tables to see all available tables.

  * **PostgreSQL:**

    ```sql
    SELECT * FROM pg_catalog.pg_tables;
    ```
  * **SQL Server:**

    ```sql
    SELECT * FROM information_schema.tables;
    ```
  * **MySQL:**

    ```sql
    SHOW TABLES;
    ```

**6. Filtering by Schema**

* To avoid irrelevant system tables, filter by your data schema:

  ```sql
  SELECT * FROM pg_catalog.pg_tables
  WHERE schemaname = 'public';
  ```

**7. Practice**

* Use these exploration techniques to locate relevant tables and columns before writing analytical queries.

---
### Joining the Correct Tables (SQL)

**1. Overview**

* In real projects, data often resides across **multiple tables** that must be correctly **joined**.
* This lesson introduces a tool to explore tables and columns efficiently before building joins.

**2. Exploring All Tables & Columns**

* Use the **`information_schema.columns`** system table — it lists every **column** in every **table** in your database.

  ```sql
  SELECT * FROM information_schema.columns;
  ```

* This works across major SQL systems (PostgreSQL, MySQL, SQL Server).

**3. Filtering for Relevant Schemas**

* System schemas (like `pg_catalog`) contain metadata, not useful data.
* Filter to only include user data (e.g., `public` schema):

  ```sql
  SELECT * 
  FROM information_schema.columns
  WHERE table_schema = 'public';
  ```

**4. Aggregating Columns per Table**

* To make exploration easier, aggregate all columns of a table into a single string using **`STRING_AGG()`**:

  ```sql
  SELECT table_name,
         STRING_AGG(column_name, ', ') AS columns
  FROM information_schema.columns
  WHERE table_schema = 'public'
  GROUP BY table_name;
  ```
* Each row now represents a table and its columns — a quick reference view of your database structure.

**5. Creating a Reusable VIEW**

* Save this query as a **VIEW** (a virtual table) for easy future access:

  ```sql
  CREATE VIEW table_columns AS
  SELECT table_name,
         STRING_AGG(column_name, ', ') AS columns
  FROM information_schema.columns
  WHERE table_schema = 'public'
  GROUP BY table_name;
  ```

* Once created, query it like any other table:

  ```sql
  SELECT * FROM table_columns;
  ```

**6. Practice**

* Use your new `table_columns` view to quickly locate tables and columns before writing join queries.

---

### Complex Joins (SQL)

**1. Overview**

* Real-world data is often **scattered across multiple tables**.
* To answer complex questions, you must **identify relationships** and **join** the right tables.

**2. Example Scenario**

* Question: *“How many videos were rented in each city?”*
* Needed data:

  * **rental table** → rental details
  * **address table** → city information
  * **customer table** → links rental and address tables

**3. Building Table Connections**

* **rental** and **address** have no direct link.
* **customer** acts as the **bridge** with:

  * `customer_id` → connects to `rental`
  * `address_id` → connects to `address`
* Joining these three tables provides the data needed to summarize rentals by city.

  ```sql
  SELECT city, COUNT(rental_id) AS total_rentals
  FROM rental
  JOIN customer ON rental.customer_id = customer.customer_id
  JOIN address ON customer.address_id = address.address_id
  JOIN city ON address.city_id = city.city_id
  GROUP BY city;
  ```

**4. Entity Relationship Diagram (ERD)**

* An **ERD** visually maps how tables connect via key columns.
* Helps you understand database structure and plan joins effectively.
* You can create your own ERD when one isn’t provided.

**5. Key Takeaway**

* Use your **table exploration tools** (`information_schema`, `table_columns` view, etc.) to:

  * Identify which tables hold relevant data.
  * Determine **how** they relate.
  * Combine them through **joins** to answer multi-table questions.

---




