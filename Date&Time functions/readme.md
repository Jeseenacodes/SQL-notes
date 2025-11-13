
 **Date and Time Functions in MySQL**

```sql
-- This script demonstrates key MySQL date and time functions using the 'sakila' database.

------------------------------------------------------------
-- 1️⃣ Current Date and Time Functions

-- NOW() returns the date and time at the start of the query execution.
SELECT NOW();
SELECT NOW(), SLEEP(2), NOW();  -- Both NOW() values will be the same.

-- SYSDATE() returns the date and time at the moment the function executes.
SELECT SYSDATE();
SELECT SYSDATE(), SLEEP(2), SYSDATE();  -- SYSDATE() values will differ by ~2 seconds.

-- CURRENT_DATE() returns only the current date.
SELECT CURRENT_DATE();

-- CURRENT_TIME() returns only the current time.
SELECT CURRENT_TIME();

------------------------------------------------------------
-- 2️⃣ Exploring Date Parts from a Column

-- Switch to the Sakila database.
USE sakila;

-- View the FILM table structure.
SELECT * FROM film;

-- Extract specific parts of the 'last_update' datetime column.
SELECT 
    SECOND(last_update)     AS second_value,
    MINUTE(last_update)     AS minute_value,
    HOUR(last_update)       AS hour_value,
    DAY(last_update)        AS day_value,
    DAYNAME(last_update)    AS weekday_name,
    WEEK(last_update)       AS week_number,
    MONTH(last_update)      AS month_number,
    MONTHNAME(last_update)  AS month_name,
    YEAR(last_update)       AS year_value,
    TIMESTAMP(last_update)  AS full_timestamp
FROM film;

------------------------------------------------------------
-- 3️⃣ Date Difference Calculation

-- DATEDIFF() subtracts two dates and returns the difference in days.
-- Example: How many years since the last film update?
SELECT 
    DATEDIFF(NOW(), (SELECT last_update FROM film LIMIT 1)) / 365 AS years_since_update;

-- Preview a sample date used above.
SELECT last_update 
FROM film 
LIMIT 1;
```

---

##  **MySQL Date & Time Functions**

### **1️⃣ Current Date and Time Functions**

| Function         | Description                                           | Example                  | Notes                                              |
| ---------------- | ----------------------------------------------------- | ------------------------ | -------------------------------------------------- |
| `NOW()`          | Returns current date & time **at query start**        | `SELECT NOW();`          | Same timestamp even after delays (e.g., `SLEEP()`) |
| `SYSDATE()`      | Returns current date & time **at function execution** | `SELECT SYSDATE();`      | Changes with each call                             |
| `CURRENT_DATE()` | Returns current date only                             | `SELECT CURRENT_DATE();` | Equivalent to `CURDATE()`                          |
| `CURRENT_TIME()` | Returns current time only                             | `SELECT CURRENT_TIME();` | Equivalent to `CURTIME()`                          |

---

### **2️⃣ Extracting Date Parts from a Column**

Use these to break down a `DATETIME` column such as `last_update` in the **Sakila** database:

| Function      | Purpose                | Example                  | Output Example      |
| ------------- | ---------------------- | ------------------------ | ------------------- |
| `SECOND()`    | Extracts seconds       | `SECOND(last_update)`    | 42                  |
| `MINUTE()`    | Extracts minutes       | `MINUTE(last_update)`    | 15                  |
| `HOUR()`      | Extracts hours         | `HOUR(last_update)`      | 13                  |
| `DAY()`       | Extracts day of month  | `DAY(last_update)`       | 25                  |
| `DAYNAME()`   | Returns weekday name   | `DAYNAME(last_update)`   | "Wednesday"         |
| `WEEK()`      | Returns week number    | `WEEK(last_update)`      | 34                  |
| `MONTH()`     | Returns month number   | `MONTH(last_update)`     | 11                  |
| `MONTHNAME()` | Returns month name     | `MONTHNAME(last_update)` | "November"          |
| `YEAR()`      | Returns year           | `YEAR(last_update)`      | 2025                |
| `TIMESTAMP()` | Returns datetime value | `TIMESTAMP(last_update)` | 2025-11-07 10:30:00 |

---

### **3️⃣ Date Difference**

| Function                       | Description                                      | Example                                          | Result       |
| ------------------------------ | ------------------------------------------------ | ------------------------------------------------ | ------------ |
| `DATEDIFF(date1, date2)`       | Returns the **number of days** between two dates | `SELECT DATEDIFF(NOW(), last_update) FROM film;` | e.g., `180`  |
| Approximate years since update | Divide by 365                                    | `DATEDIFF(NOW(), last_update)/365`               | e.g., `0.49` |

---

### **Key Takeaways**

* `NOW()` is fixed; `SYSDATE()` is real-time.
* Use **aliases (`AS`)** for readability when extracting multiple date parts.
* `DATEDIFF()` Only counts *whole days*; divide by 365 for approximate years.
* `DAYNAME()` and `MONTHNAME()` help make reports more readable.

---
