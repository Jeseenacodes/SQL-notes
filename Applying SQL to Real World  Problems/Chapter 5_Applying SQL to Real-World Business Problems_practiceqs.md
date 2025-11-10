
## **SQL Training Portfolio: Applying SQL to Real-World Business Problems**

**Database Used:** Pagila (DVD Rental Database)
**Objective:** Apply SQL to solve real-world data analysis, management, and reporting tasks — from data extraction and transformation to database structure management and performance optimization.

### **1. Data Transformation & Cleaning**

#### **Transform Numeric & String Data**

* Converted film titles to lowercase using `LOWER()`.
* Filtered films released before 2006 and calculated sale prices (50% off).

  ```sql
  SELECT LOWER(title) AS title, 
         rental_rate AS original_rate, 
         rental_rate * 0.5 AS sale_rate 
  FROM film
  WHERE release_year < 2006;
  ```

#### **Extract Key Date/Time Elements**

* Extracted **day**, **year**, and **hour** from payment timestamps using `EXTRACT()`.

  ```sql
  SELECT payment_date, 
         EXTRACT(DAY FROM payment_date) AS payment_day
  FROM payment;
  ```

### **2. Aggregation & Financial Insights**

#### **Aggregate Financial Metrics**

* Compared **active vs inactive customers** using `COUNT()`, `AVG()`, and `SUM()` to measure engagement and revenue.

  ```sql
  SELECT active, COUNT(payment_id), AVG(amount), SUM(amount)
  FROM payment AS p
  INNER JOIN customer AS c
    ON p.customer_id = c.customer_id
  GROUP BY active;
  ```

#### **Aggregate Strings for Storefront Display**

* Showcased **G-rated film titles** by language for promotional displays using `STRING_AGG()`.

  ```sql
  SELECT name, STRING_AGG(title, ', ') AS film_titles
  FROM film AS f
  INNER JOIN language AS l ON f.language_id = l.language_id
  WHERE release_year = 2010 AND rating = 'G'
  GROUP BY name;
  ```

### **3. Data Exploration & Discovery**

#### **Limit Queries for Quick Exploration**

* Used `LIMIT` to preview data:

  ```sql
  SELECT * FROM payment LIMIT 10;
  ```
* Identified **top 10 payments by amount** using `ORDER BY ... DESC LIMIT`.

#### **Find Tables & Columns**

* Explored schema using **system catalog tables** (`pg_catalog.pg_tables`, `information_schema.columns`).

  ```sql
  SELECT table_name, column_name
  FROM information_schema.columns
  WHERE table_schema = 'public';
  ```

#### **Created a Reusable Database View**

* Built a `VIEW` combining table and column names for quick schema reference.

  ```sql
  CREATE VIEW table_columns AS
  SELECT table_name, STRING_AGG(column_name, ', ') AS columns
  FROM information_schema.columns
  WHERE table_schema = 'public'
  GROUP BY table_name;
  ```

### **4. Business Analytics Queries**

#### **Monthly Income**

* Summed up payments per month:

  ```sql
  SELECT EXTRACT(MONTH FROM payment_date) AS month, 
         SUM(amount) AS total_payment
  FROM payment
  GROUP BY month;
  ```

#### **Average Film Length by Category**

* Used `JOIN`, `AVG()`, and `ORDER BY` to rank film categories by duration.

  ```sql
  SELECT c.name AS category, AVG(f.length) AS average_length
  FROM film AS f
  INNER JOIN film_category AS fc ON f.film_id = fc.film_id
  INNER JOIN category AS c ON fc.category_id = c.category_id
  GROUP BY c.name
  ORDER BY average_length;
  ```

#### **Most Frequently Rented Films**

* Combined `film`, `inventory`, and `rental` tables to identify high-demand titles.

  ```sql
  SELECT title, COUNT(title) AS rentals
  FROM film AS f
  INNER JOIN inventory AS i ON f.film_id = i.film_id
  INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
  GROUP BY title
  ORDER BY rentals DESC;
  ```

### **5. Database Management**

#### **Creating New Tables**

* Created and populated a new `oscars` table for award-winning films.

  ```sql
  CREATE TABLE oscars (title VARCHAR, award VARCHAR);
  INSERT INTO oscars VALUES ('MARS ROMAN', 'Best Film'), ...;
  ```

#### **Creating Tables from Queries**

* Built a `family_films` table containing only G and PG-rated films.

  ```sql
  CREATE TABLE family_films AS
  SELECT * FROM film WHERE rating IN ('G', 'PG');
  ```

#### **Updating Records**

* Increased all rental prices by $0.50 and R-rated films by an additional $1.

  ```sql
  UPDATE film SET rental_rate = rental_rate + 0.5;
  UPDATE film SET rental_rate = rental_rate + 1 WHERE rating = 'R';
  ```

* Decreased rental rates for films starring select actors using a subquery.

#### **Deleting Records**

* Removed expensive or inappropriate films:

  ```sql
  DELETE FROM film WHERE replacement_cost > 25;
  DELETE FROM film WHERE rating IN ('R', 'NC-17');
  ```
  
### **6. Query Readability & Best Practices**

* Used **consistent capitalization**, **indentation**, and **snake_case**.
* Replaced multiple `OR` conditions with `IN` lists.
* Added concise, meaningful **comments** to clarify logic.

  ```sql
  SELECT r.customer_id, r.rental_date, r.return_date 
  FROM rental AS r
  /* inventory table links rental and film tables */ 
  INNER JOIN inventory AS i ON r.inventory_id = i.inventory_id
  INNER JOIN film AS f ON i.film_id = f.film_id
  WHERE f.length < 90;
  ```

### **Key Takeaways**

* Gained practical experience in **data transformation, aggregation, and schema management**.
* Learned to **create, update, and delete tables** safely.
* Applied **best practices** for clean, maintainable SQL.
* Developed queries that provide actionable business insights.

---

| Category                                  | Your original queries covered                                                                          |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **1. Data Transformation & Cleaning**     | LOWER(), ROUND(), EXTRACT(), basic filtering (release_year, rental_rate, etc.)                         |
| **2. Aggregation & Financial Insights**   | COUNT(), AVG(), SUM(), GROUP BY active, STRING_AGG(), comparisons by customer activity                 |
| **3. Data Exploration**                   | LIMIT, ORDER BY DESC, schema exploration via `information_schema`, CREATE VIEW for table/column lookup |
| **4. Business Analytics Queries**         | Monthly income, average film length by category, most rented films                                     |
| **5. Database Management**                | CREATE TABLE, INSERT INTO, UPDATE, DELETE, CREATE TABLE AS SELECT, subquery-based updates              |
| **6. Query Readability & Best Practices** | Use of IN vs OR, consistent indentation, aliasing, JOIN comments                                       |

