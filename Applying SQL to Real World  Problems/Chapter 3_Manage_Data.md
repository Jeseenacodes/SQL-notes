###  Storing Data (SQL)

**1. Overview**

* After learning how to find and process data, this chapter teaches how to **store results** for future use.
* In SQL, data can be stored in three main ways:

  1. Create a **new table** with new data.
  2. Create a **new table** from an existing query.
  3. Create a **view** from an existing query.

**2. Creating a Table with New Data**

* Used when the data you need doesn’t exist yet (e.g., adding a table for customer distance data).
* Steps:

  1. **Create an empty table** and define columns with data types.

     ```sql
     CREATE TABLE customer_distance (
       postal_code INT,
       distance FLOAT
     );
     ```
  2. **Insert data** into the table.

     ```sql
     INSERT INTO customer_distance (postal_code, distance)
     VALUES (94110, 3.4), (94117, 1.2);
     ```

**3. Creating a Table from Existing Data**

* Used to save query results as a **new table**.
* Example: Saving all “G-rated” films.

  ```sql
  CREATE TABLE family_films AS
  SELECT film_id, title
  FROM film
  WHERE rating = 'G';
  ```
* Notes:

  * Data types are inherited from the source columns.
  * Acts as a **snapshot** — changes in the source data won’t affect this table.

**4. Creating a View from Existing Data**

* A **VIEW** saves a **query**, not the actual data.
* Example:

  ```sql
  CREATE VIEW family_films_view AS
  SELECT film_id, title
  FROM film
  WHERE rating = 'G';
  ```

**5. TABLE vs VIEW**

| Feature      | TABLE                                   | VIEW                                             |
| ------------ | --------------------------------------- | ------------------------------------------------ |
| Stores       | Data snapshot                           | Query definition                                 |
| Updates      | Static unless manually changed          | Always reflects latest source data               |
| Modification | Directly editable                       | Indirect (through underlying tables)             |
| Use case     | When you need fixed or derived datasets | When you need dynamic, always up-to-date results |

---

**6. Practice**

* Try creating both **tables** and **views** from your queries to understand how stored data behaves differently in each case.

---

### Updating Data (SQL)

**1. Overview**

* After learning how to create and populate tables, this lesson teaches how to **modify existing records** in a database using the `UPDATE` statement.

**2. UPDATE Syntax**

* Basic structure:

  ```sql
  UPDATE table_name
  SET column_name = new_value
  WHERE condition;
  ```
* The `SET` clause defines which columns to update and what new values to assign.
* Always use a `WHERE` clause to control which rows are modified.

**3. Example: Update a Column**

* Convert all customer emails to lowercase:

  ```sql
  UPDATE customer
  SET email = LOWER(email);
  ```

**4. Example: Update with a Condition**

* Update only *active* customers:

  ```sql
  UPDATE customer
  SET email = LOWER(email)
  WHERE active = TRUE;
  ```

**5. Example: Update Using a Subquery**

* Update only customers from a specific city (e.g., *Woodridge*):

  ```sql
  UPDATE customer
  SET email = LOWER(email)
  WHERE address_id IN (
      SELECT address_id
      FROM address
      WHERE city = 'Woodridge'
  );
  ```

**6. Best Practices & Precautions**

* **Always double-check before updating data.**

  * ✅ Ensure you have **proper permissions**.
  * ✅ Understand **how the table is used** and potential downstream impacts.
  * ✅ Test your `WHERE` clause first with a `SELECT` query:

    ```sql
    SELECT * FROM customer WHERE active = TRUE;
    ```
* Incorrect updates can permanently affect data integrity and reports.

**7. Practice**

* Use `UPDATE` with different conditions and subqueries to safely modify specific records in your tables.

---

### Deleting  Data (SQL)

**1. Overview**

* After learning how to **create** and **update** tables, this lesson covers how to **remove data or entire tables** from your database safely.


**2. Commands for Deleting Data**
There are **three main SQL commands** for data deletion, each serving a different purpose:

| Command                                   | Description                                                         | Effect                                 |
| ----------------------------------------- | ------------------------------------------------------------------- | -------------------------------------- |
| `DROP TABLE table_name;`                  | Completely removes the table and its data from the database.        | Deletes both structure & data.         |
| `TRUNCATE TABLE table_name;`              | Deletes **all records** from a table but keeps the table structure. | Keeps structure, clears data.          |
| `DELETE FROM table_name WHERE condition;` | Deletes **specific rows** that meet a condition.                    | Keeps both structure & remaining data. |

---

**3. Example: Delete Inactive Customers**

* Remove customers who are not active:

  ```sql
  DELETE FROM customer
  WHERE active = FALSE;
  ```

**4. Example: Delete Using a Subquery**

* Delete records based on data in another table (e.g., customers from *Woodridge*):

  ```sql
  DELETE FROM customer
  WHERE address_id IN (
      SELECT address_id
      FROM address
      WHERE city = 'Woodridge'
  );
  ```

**5. Best Practices & Cautions**

* **Be extremely careful** with `DELETE`, `TRUNCATE`, and `DROP`.

  * ✅ Always verify your `WHERE` clause with a `SELECT` first.
  * ✅ Confirm permissions before running destructive commands.
  * ✅ Understand the downstream impact of removing data.
* **Tip:** Use `BEGIN TRANSACTION` (if supported) to safely test deletions before committing.

**6. Practice**

* Try deleting rows, clearing tables, and dropping tables in a safe, sandbox environment.

---
