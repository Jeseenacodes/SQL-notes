
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

### Notes:

* **`NOW()` vs `SYSDATE()`** → NOW() is fixed at query start; SYSDATE() changes during execution.
* **`DATEDIFF()`** → Only counts *whole days*; divide by 365 for approximate years.
* **Functions like `MONTHNAME()` and `DAYNAME()`** are great for human-readable reports.
* Always use **aliases (`AS`)** for clarity when selecting multiple derived columns.

---

Would you like me to make this into a **PDF titled “MySQL Date & Time Functions Practice”** (in the same style as your SQL Training Portfolio)?
